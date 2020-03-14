---
title: Diseño de CollectionView de Xamarin. Forms
description: De forma predeterminada, una CollectionView mostrará sus elementos en una lista vertical. Sin embargo, se pueden especificar listas y cuadrículas verticales y horizontales.
ms.prod: xamarin
ms.assetid: 5FE78207-1BD6-4706-91EF-B13932321FC9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/22/2019
ms.openlocfilehash: 6c2b3d8bad621db3110fe25041125c5694f21180
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305922"
---
# <a name="xamarinforms-collectionview-layout"></a>Diseño de CollectionView de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define las siguientes propiedades que controlan el diseño:

- [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout), de tipo [`IItemsLayout`](xref:Xamarin.Forms.IItemsLayout), especifica el diseño que se va a utilizar.
- [`ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy), de tipo [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy), especifica la estrategia de medida de elementos que se va a utilizar.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que las propiedades pueden ser destinos de enlaces de datos.

De forma predeterminada, un [`CollectionView`](xref:Xamarin.Forms.CollectionView) mostrará sus elementos en una lista vertical. Sin embargo, se puede usar cualquiera de los siguientes diseños:

- Lista vertical: una lista de una sola columna que crece verticalmente a medida que se agregan nuevos elementos.
- Lista horizontal: una lista de una sola fila que crece horizontalmente a medida que se agregan nuevos elementos.
- Cuadrícula vertical: una cuadrícula de varias columnas que crece verticalmente a medida que se agregan nuevos elementos.
- Cuadrícula horizontal: una cuadrícula de varias filas que crece horizontalmente a medida que se agregan nuevos elementos.

Estos diseños se pueden especificar estableciendo la propiedad [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) en la clase que deriva de la clase [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) . Esta clase define las siguientes propiedades:

- [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation), de tipo [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation), especifica la dirección en la que el [`CollectionView`](xref:Xamarin.Forms.CollectionView) se expande a medida que se agregan elementos.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), de tipo [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), especifica cómo se alinean los puntos de ajuste con los elementos.
- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), de tipo [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), especifica el comportamiento de los puntos de ajuste al desplazarse.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que las propiedades pueden ser destinos de enlaces de datos. Para obtener más información sobre los puntos de acoplamiento, vea [puntos de acoplamiento](scrolling.md#snap-points) en la guía de [desplazamientos de la CollectionView de Xamarin. Forms](scrolling.md) .

La enumeración [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) define los siguientes miembros:

- `Vertical` indica que el [`CollectionView`](xref:Xamarin.Forms.CollectionView) se expandirá verticalmente a medida que se agreguen elementos.
- `Horizontal` indica que el [`CollectionView`](xref:Xamarin.Forms.CollectionView) se expandirá horizontalmente a medida que se agreguen elementos.

La clase `LinearItemsLayout` hereda de la clase [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) y define una propiedad `ItemSpacing`, de tipo `double`, que representa el espacio vacío alrededor de cada elemento. El valor predeterminado de esta propiedad es 0, y su valor siempre debe ser mayor o igual que 0. La clase `LinearItemsLayout` también define los miembros `Vertical` y `Horizontal` estáticos. Estos miembros se pueden usar para crear listas verticales u horizontales, respectivamente. Como alternativa, se puede crear un objeto de `LinearItemsLayout`, especificando un miembro de enumeración [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) como un argumento.

La clase [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) hereda de la clase [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) y define las siguientes propiedades:

- `VerticalItemSpacing`, de tipo `double`, que representa el espacio vacío vertical alrededor de cada elemento. El valor predeterminado de esta propiedad es 0, y su valor siempre debe ser mayor o igual que 0.
- `HorizontalItemSpacing`, de tipo `double`, que representa el espacio vacío horizontal alrededor de cada elemento. El valor predeterminado de esta propiedad es 0, y su valor siempre debe ser mayor o igual que 0.
- `Span`, de tipo `int`, que representa el número de columnas o filas que se van a mostrar en la cuadrícula. El valor predeterminado de esta propiedad es 1 y su valor siempre debe ser mayor o igual que 1.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que las propiedades pueden ser destinos de enlaces de datos.

> [!NOTE]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) usa los motores de diseño nativo para realizar el diseño.

## <a name="vertical-list"></a>Lista vertical

De forma predeterminada, [`CollectionView`](xref:Xamarin.Forms.CollectionView) mostrará sus elementos en un diseño de lista vertical. Por lo tanto, no es necesario establecer la propiedad [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) para usar este diseño:

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

Sin embargo, por integridad, se puede establecer un [`CollectionView`](xref:Xamarin.Forms.CollectionView) para mostrar sus elementos en una lista vertical estableciendo su propiedad [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) en `VerticalList`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                ItemsLayout="VerticalList">
    ...
</CollectionView>
```

Como alternativa, también se puede lograr estableciendo la propiedad [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) en un objeto `LinearItemsLayout`, especificando el miembro de enumeración `Vertical` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) como el valor de la propiedad `Orientation`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = LinearItemsLayout.Vertical
};
```

Esto da como resultado una lista de una sola columna, que crece verticalmente a medida que se agregan nuevos elementos:

[![Captura de pantalla de un diseño de lista vertical de CollectionView, en iOS y Android](layout-images/vertical-list.png "Diseño de lista vertical de CollectionView")](layout-images/vertical-list-large.png#lightbox "Diseño de lista vertical de CollectionView")

## <a name="horizontal-list"></a>Lista horizontal

[`CollectionView`](xref:Xamarin.Forms.CollectionView) puede mostrar sus elementos en una lista horizontal estableciendo su propiedad [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) en `HorizontalList`:

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

También puede realizar este diseño estableciendo la propiedad [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) en un objeto `LinearItemsLayout`, especificando el miembro de enumeración `Horizontal` [`ItemsLayoutOrientation`](xref:Xamarin.Forms.ItemsLayoutOrientation) como el valor de la propiedad `Orientation`:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = LinearItemsLayout.Horizontal
};
```

Esto da como resultado una lista de una sola fila, que crece horizontalmente a medida que se agregan nuevos elementos:

[![Captura de pantalla de un diseño de lista horizontal de CollectionView, en iOS y Android](layout-images/horizontal-list.png "Diseño de lista horizontal de CollectionView")](layout-images/horizontal-list-large.png#lightbox "Diseño de lista horizontal de CollectionView")

## <a name="vertical-grid"></a>Cuadrícula vertical

[`CollectionView`](xref:Xamarin.Forms.CollectionView) puede mostrar sus elementos en una cuadrícula vertical estableciendo su propiedad [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) en un objeto [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) cuya propiedad [`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) esté establecida en `Vertical`:

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

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(2, ItemsLayoutOrientation.Vertical)
};
```

De forma predeterminada, un [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) vertical mostrará los elementos en una sola columna. Sin embargo, en este ejemplo se establece la propiedad `GridItemsLayout.Span` en 2. Esto da como resultado una cuadrícula de dos columnas, que crece verticalmente a medida que se agregan nuevos elementos:

[![Captura de pantalla de un diseño de cuadrícula vertical de CollectionView, en iOS y Android](layout-images/vertical-grid.png "Diseño de cuadrícula vertical de CollectionView")](layout-images/vertical-grid-large.png#lightbox "Diseño de cuadrícula vertical de CollectionView")

## <a name="horizontal-grid"></a>Cuadrícula horizontal

[`CollectionView`](xref:Xamarin.Forms.CollectionView) puede mostrar sus elementos en una cuadrícula horizontal estableciendo su propiedad [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) en un objeto [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) cuya propiedad[`Orientation`](xref:Xamarin.Forms.ItemsLayout.Orientation) esté establecida en `Horizontal`:

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

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new GridItemsLayout(4, ItemsLayoutOrientation.Horizontal)
};
```

De forma predeterminada, un [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) horizontal mostrará los elementos en una sola fila. Sin embargo, en este ejemplo se establece la propiedad `GridItemsLayout.Span` en 4. Esto da como resultado una cuadrícula de cuatro filas, que crece horizontalmente a medida que se agregan nuevos elementos:

[![Captura de pantalla de un diseño de cuadrícula horizontal de CollectionView, en iOS y Android](layout-images/horizontal-grid.png "Diseño de cuadrícula horizontal de CollectionView")](layout-images/horizontal-grid-large.png#lightbox "Diseño de cuadrícula horizontal de CollectionView")

## <a name="headers-and-footers"></a>Encabezados y pies de página

[`CollectionView`](xref:Xamarin.Forms.CollectionView) puede presentar un encabezado y un pie de página que se desplacen con los elementos de la lista. El encabezado y el pie de página pueden ser cadenas, vistas u objetos [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) .

[`CollectionView`](xref:Xamarin.Forms.CollectionView) define las siguientes propiedades para especificar el encabezado y el pie de página:

- `Header`, de tipo `object`, especifica la cadena, el enlace o la vista que se mostrarán al principio de la lista.
- `HeaderTemplate`, de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), especifica el `DataTemplate` que se va a utilizar para dar formato al `Header`.
- `Footer`, de tipo `object`, especifica la cadena, el enlace o la vista que se mostrarán al final de la lista.
- `FooterTemplate`, de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), especifica el `DataTemplate` que se va a utilizar para dar formato al `Footer`.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que las propiedades pueden ser destinos de enlaces de datos.

Cuando se agrega un encabezado a un diseño que crece horizontalmente, de izquierda a derecha, el encabezado se muestra a la izquierda de la lista. Del mismo modo, cuando un pie de página se agrega a un diseño que crece horizontalmente, de izquierda a derecha, el pie de página se muestra a la derecha de la lista.

### <a name="display-strings-in-the-header-and-footer"></a>Mostrar cadenas en el encabezado y el pie de página

Las propiedades `Header` y `Footer` se pueden establecer en valores `string`, como se muestra en el ejemplo siguiente:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}"
                Header="Monkeys"
                Footer="2019">
    ...
</CollectionView>
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    Header = "Monkeys",
    Footer = "2019"
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Monkeys");
```

Este código genera las siguientes capturas de pantalla, con el encabezado que se muestra en la captura de pantalla de iOS y el pie de página que se muestra en la captura de pantalla de Android:

[![Captura de pantalla de un encabezado y un pie de cadena de CollectionView, en iOS y Android](layout-images/header-footer-string.png "Encabezado y pie de cadena de CollectionView")](layout-images/header-footer-string-large.png#lightbox "Encabezado y pie de cadena de CollectionView")

### <a name="display-views-in-the-header-and-footer"></a>Mostrar vistas en el encabezado y el pie de página

Cada una de las propiedades `Header` y `Footer` se puede establecer en una vista. Puede ser una vista única o una vista que contiene varias vistas secundarias. En el ejemplo siguiente se muestran las propiedades `Header` y `Footer` establecidas cada una de ellas en un objeto [`StackLayout`](xref:Xamarin.Forms.StackLayout) que contiene un objeto [`Label`](xref:Xamarin.Forms.Label) :

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

El código de C# equivalente es el siguiente:

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

[![Captura de pantalla de un encabezado y pie de página de CollectionView con vistas, en iOS y Android](layout-images/header-footer-view.png "Encabezado y pie de página de la vista CollectionView")](layout-images/header-footer-view-large.png#lightbox "Encabezado y pie de página de la vista CollectionView")

### <a name="display-a-templated-header-and-footer"></a>Mostrar un encabezado y un pie de página con plantilla

Las propiedades `HeaderTemplate` y `FooterTemplate` se pueden establecer en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objetos que se utilizan para dar formato al encabezado y al pie de página. En este escenario, las propiedades `Header` y `Footer` deben enlazar con el origen actual de las plantillas que se van a aplicar, tal como se muestra en el ejemplo siguiente:

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

El código de C# equivalente es el siguiente:

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

[![Captura de pantalla de un encabezado y pie de página de CollectionView con plantillas, en iOS y Android](layout-images/header-footer-template.png "Encabezado y pie de plantilla de CollectionView")](layout-images/header-footer-template-large.png#lightbox "Encabezado y pie de plantilla de CollectionView")

## <a name="item-spacing"></a>Espaciado de elementos

De forma predeterminada, cada elemento de una [`CollectionView`](xref:Xamarin.Forms.CollectionView) no tiene espacio vacío. Este comportamiento se puede cambiar estableciendo las propiedades en el diseño de los elementos utilizados por el `CollectionView`.

Cuando un [`CollectionView`](xref:Xamarin.Forms.CollectionView) establece su propiedad [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) en un objeto `LinearItemsLayout`, la propiedad `LinearItemsLayout.ItemSpacing` se puede establecer en un valor `double` que representa el espacio vacío alrededor de cada elemento:

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           ItemSpacing="20" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

> [!NOTE]
> La propiedad `LinearItemsLayout.ItemSpacing` tiene un conjunto de devoluciones de llamada de validación, que garantiza que el valor de la propiedad sea siempre mayor o igual que 0.

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        ItemSpacing = 20
    }
};
```

Este código genera una lista de una sola columna vertical, que tiene un espaciado de 20 alrededor de cada elemento:

[![Captura de pantalla de una CollectionView con espaciado de elementos, en iOS y Android](layout-images/vertical-list-spacing.png "Espaciado de elementos de CollectionView")](layout-images/vertical-list-spacing-large.png#lightbox "Espaciado de elementos de CollectionView")

Cuando un [`CollectionView`](xref:Xamarin.Forms.CollectionView) establece su propiedad [`ItemsLayout`](xref:Xamarin.Forms.StructuredItemsView.ItemsLayout) en un objeto [`GridItemsLayout`](xref:Xamarin.Forms.GridItemsLayout) , las propiedades `GridItemsLayout.VerticalItemSpacing` y `GridItemsLayout.HorizontalItemSpacing` se pueden establecer en `double` valores que representan el espacio vacío vertical y horizontalmente alrededor de cada elemento:

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
> Las propiedades `GridItemsLayout.VerticalItemSpacing` y `GridItemsLayout.HorizontalItemSpacing` tienen establecidas devoluciones de llamada de validación, que garantizan que los valores de las propiedades sean siempre mayores o iguales que 0.

El código de C# equivalente es el siguiente:

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

[![Captura de pantalla de una CollectionView con espaciado de elementos, en Android](layout-images/vertical-grid-spacing.png "Espaciado de elementos de CollectionView")](layout-images/vertical-grid-spacing-large.png#lightbox "Espaciado de elementos de CollectionView")

## <a name="item-sizing"></a>Tamaño del elemento

De forma predeterminada, cada elemento de un [`CollectionView`](xref:Xamarin.Forms.CollectionView) se mide y ajusta de forma individual, siempre que los elementos de la interfaz de usuario del [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) no especifiquen tamaños fijos. Este comportamiento, que se puede cambiar, se especifica mediante el valor de la propiedad [`CollectionView.ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy) . Este valor de propiedad se puede establecer en uno de los miembros de enumeración de [`ItemSizingStrategy`](xref:Xamarin.Forms.ItemSizingStrategy) :

- `MeasureAllItems`: cada elemento se mide individualmente. Este es el valor predeterminado.
- `MeasureFirstItem`: solo se mide el primer elemento, donde todos los elementos subsiguientes reciben el mismo tamaño que el primer elemento.

> [!IMPORTANT]
> La estrategia de ajuste de tamaño de `MeasureFirstItem` dará lugar a un aumento del rendimiento cuando se usa en situaciones en las que el tamaño del elemento está diseñado para ser uniforme en todos los elementos.

En el ejemplo de código siguiente se muestra el establecimiento de la propiedad [`ItemSizingStrategy`](xref:Xamarin.Forms.StructuredItemsView.ItemSizingStrategy) :

```xaml
<CollectionView ...
                ItemSizingStrategy="MeasureFirstItem">
    ...
</CollectionView>
```

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    ...
    ItemSizingStrategy = ItemSizingStrategy.MeasureFirstItem
};
```

## <a name="dynamic-resizing-of-items"></a>Cambio de tamaño dinámico de los elementos

Los elementos de un [`CollectionView`](xref:Xamarin.Forms.CollectionView) pueden cambiar de tamaño dinámicamente en tiempo de ejecución cambiando las propiedades relacionadas con el diseño de los elementos de la [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Por ejemplo, en el ejemplo de código siguiente se cambian las propiedades [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) y [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) de un objeto [`Image`](xref:Xamarin.Forms.Image) :

```csharp
void OnImageTapped(object sender, EventArgs e)
{
    Image image = sender as Image;
    image.HeightRequest = image.WidthRequest = image.HeightRequest.Equals(60) ? 100 : 60;
}
```

El controlador de eventos `OnImageTapped` se ejecuta como respuesta a un objeto [`Image`](xref:Xamarin.Forms.Image) que se está punteando y cambia las dimensiones de la imagen para que se vea más fácilmente:

[![Captura de pantalla de una CollectionView con el ajuste de tamaño dinámico de elementos, en iOS y Android](layout-images/runtime-resizing.png "Tamaño de elemento dinámico de CollectionView")](layout-images/runtime-resizing-large.png#lightbox "Tamaño de elemento dinámico de CollectionView")

## <a name="right-to-left-layout"></a>Diseño de derecha a izquierda

[`CollectionView`](xref:Xamarin.Forms.CollectionView) puede diseñar su contenido en una dirección de flujo de derecha a izquierda estableciendo su propiedad [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) en [`RightToLeft`](xref:Xamarin.Forms.FlowDirection.RightToLeft). Sin embargo, la propiedad `FlowDirection` debe establecerse idealmente en un diseño de página o raíz, lo que hace que todos los elementos de la página o el diseño raíz respondan a la dirección del flujo:

```xaml
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

La [`FlowDirection`](xref:Xamarin.Forms.VisualElement.FlowDirection) predeterminada para un elemento con un elemento primario es [`MatchParent`](xref:Xamarin.Forms.FlowDirection.MatchParent). Por lo tanto, el [`CollectionView`](xref:Xamarin.Forms.CollectionView) hereda el valor de la propiedad `FlowDirection` de la [`StackLayout`](xref:Xamarin.Forms.StackLayout), que a su vez hereda el valor de la propiedad `FlowDirection` de [`ContentPage`](xref:Xamarin.Forms.ContentPage). Esto da como resultado el diseño de derecha a izquierda que se muestra en las siguientes capturas de pantallas:

[![Captura de pantalla de un diseño de lista vertical de derecha a izquierda de CollectionView, en iOS y Android](layout-images/vertical-list-rtl.png "Diseño de lista vertical de derecha a izquierda de CollectionView")](layout-images/vertical-list-rtl-large.png#lightbox "Diseño de lista vertical de derecha a izquierda de CollectionView")

Para obtener más información acerca de la dirección de flujo, consulte [localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md).

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Localización de derecha a izquierda](~/xamarin-forms/app-fundamentals/localization/right-to-left.md)
- [Desplazamientos de CollectionView de Xamarin. Forms](scrolling.md)
