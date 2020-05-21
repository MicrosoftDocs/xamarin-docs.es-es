---
title: Cuadrícula de Xamarin. Forms
description: La cuadrícula de Xamarin. Forms es un diseño que organiza sus elementos secundarios en filas y columnas de celdas.
ms.prod: xamarin
ms.assetid: 762B1802-D185-494C-B643-74EED55882FE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/15/2020
ms.openlocfilehash: 4f1d9d0f2d597018b9832d918bbec3f0b2594773
ms.sourcegitcommit: bc0c1740aa0708459729c0e671ab3ff7de3e2eee
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/15/2020
ms.locfileid: "83425911"
---
# <a name="xamarinforms-grid"></a>Cuadrícula de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-griddemos)

[![Cuadrícula de Xamarin. Forms](grid-images/layouts.png "Cuadrícula de Xamarin. Forms")](grid-images/layouts-large.png#lightbox "Cuadrícula de Xamarin. Forms")

[`Grid`](xref:Xamarin.Forms.Grid)Es un diseño que organiza sus elementos secundarios en filas y columnas, que pueden tener tamaños proporcionales o absolutos. De forma predeterminada, un `Grid` contiene una fila y una columna. Además, `Grid` se puede utilizar como un diseño primario que contiene otros diseños secundarios.

El [`Grid`](xref:Xamarin.Forms.Grid) diseño no se debe confundir con las tablas y no está diseñado para presentar datos tabulares. A diferencia de las tablas HTML, una `Grid` está pensada para diseñar el contenido. Para Mostrar datos tabulares, considere la posibilidad de usar un [control ListView](~/xamarin-forms/user-interface/listview/index.md), [CollectionView](~/xamarin-forms/user-interface/collectionview/index.md)o [TableView](~/xamarin-forms/user-interface/tableview.md).

La [`Grid`](xref:Xamarin.Forms.Grid) clase define las siguientes propiedades:

- [`Column`](xref:Xamarin.Forms.Grid.ColumnProperty), de tipo `int` , que es una propiedad adjunta que indica la alineación de las columnas de una vista dentro de un elemento primario `Grid` . El valor predeterminado de esta propiedad es 0. Una devolución de llamada de validación garantiza que, cuando se establece la propiedad, su valor es mayor o igual que 0.
- [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions), de tipo [`ColumnDefinitionCollection`](xref:Xamarin.Forms.ColumnDefinitionCollection) , es una lista de [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) objetos que definen el ancho de las columnas de la cuadrícula.
- [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing), de tipo `double` , indica la distancia entre las columnas de la cuadrícula. El valor predeterminado de esta propiedad es 6 unidades independientes del dispositivo.
- [`ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty), de tipo `int` , que es una propiedad adjunta que indica el número total de columnas que una vista abarca dentro de un elemento primario `Grid` . El valor predeterminado de esta propiedad es 1. Una devolución de llamada de validación garantiza que, cuando se establece la propiedad, su valor es mayor o igual que 1.
- [`Row`](xref:Xamarin.Forms.Grid.RowProperty), de tipo `int` , que es una propiedad adjunta que indica la alineación de filas de una vista dentro de un elemento primario `Grid` . El valor predeterminado de esta propiedad es 0. Una devolución de llamada de validación garantiza que, cuando se establece la propiedad, su valor es mayor o igual que 0.
- [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions), de tipo [`RowDefinitionCollection`](xref:Xamarin.Forms.RowDefinitionCollection) , es una lista de [`RowDefintion`](xref:Xamarin.Forms.RowDefinition) objetos que definen el alto de las filas de la cuadrícula.
- [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing), de tipo `double` , indica la distancia entre las filas de la cuadrícula. El valor predeterminado de esta propiedad es 6 unidades independientes del dispositivo.
- [`RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty), de tipo `int` , que es una propiedad adjunta que indica el número total de filas que una vista abarca dentro de un elemento primario `Grid` . El valor predeterminado de esta propiedad es 1. Una devolución de llamada de validación garantiza que, cuando se establece la propiedad, su valor es mayor o igual que 1.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos y con estilo.

La [`Grid`](xref:Xamarin.Forms.Grid) clase se deriva de la `Layout<T>` clase, que define una `Children` propiedad de tipo `IList<T>` . La `Children` propiedad es `ContentProperty` de la `Layout<T>` clase y, por tanto, no es necesario establecer explícitamente desde XAML.

> [!TIP]
> Para obtener el mejor rendimiento posible del diseño, siga las instrucciones que se indican en [optimizar el rendimiento del diseño](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance).

## <a name="rows-and-columns"></a>Filas y columnas

De forma predeterminada, un [`Grid`](xref:Xamarin.Forms.Grid) contiene una fila y una columna:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridTutorial.MainPage">
    <Grid Margin="20,35,20,20">
        <Label Text="By default, a Grid contains one row and one column." />
    </Grid>
</ContentPage>
```

En este ejemplo, [`Grid`](xref:Xamarin.Forms.Grid) contiene un único elemento secundario [`Label`](xref:Xamarin.Forms.Label) que se coloca automáticamente en una única ubicación:

[![Captura de pantalla de un diseño de cuadrícula predeterminado](grid-images/default.png "Diseño de cuadrícula predeterminado")](grid-images/default-large.png#lightbox "Diseño de cuadrícula predeterminado")

El comportamiento de diseño de [`Grid`](xref:Xamarin.Forms.Grid) se puede definir con las [`RowDefinitions`](xref:Xamarin.Forms.Grid.RowDefinitions) [`ColumnDefinitions`](xref:Xamarin.Forms.Grid.ColumnDefinitions) propiedades y, que son colecciones de [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) objetos y, respectivamente. Estas colecciones definen las características de fila y columna de un `Grid` , y deben contener un [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) objeto para cada fila de `Grid` , y un [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) objeto para cada columna en `Grid` .

La [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) clase define una [`Height`](xref:Xamarin.Forms.RowDefinition.Height) propiedad, de tipo [`GridLength`](xref:Xamarin.Forms.GridLength) , y la [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) clase define una [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) propiedad, de tipo [`GridLength`](xref:Xamarin.Forms.GridLength) . El [`GridLength`](xref:Xamarin.Forms.GridLength) struct especifica un alto de fila o un ancho de columna en cuanto a la [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) enumeración, que tiene tres miembros:

- `Absolute`: el alto de fila o el ancho de columna es un valor en unidades independientes del dispositivo (un número en XAML).
- `Auto`: el alto de fila o el ancho de columna se ajusta automáticamente según el contenido de la celda ( `Auto` en XAML).
- `Star`– el alto de fila o el ancho de columna sobrante se asigna proporcionalmente (un número seguido `*` de en XAML).

Una [`Grid`](xref:Xamarin.Forms.Grid) fila con una `Height` propiedad de `Auto` restringe el alto de las vistas en esa fila de la misma manera que una vertical [`StackLayout`](xref:Xamarin.Forms.StackLayout) . Del mismo modo, una columna con una `Width` propiedad de funciona de forma `Auto` muy parecida a una horizontal `StackLayout` .

> [!CAUTION]
> Intente asegurarse de que el tamaño de las filas y columnas es el máximo posible [`Auto`](xref:Xamarin.Forms.GridLength.Auto) . Cada fila o columna de tamaño automático hará que el motor de diseño tenga que realizar cálculos de diseño adicionales. En su lugar, use filas y columnas de tamaño fijo si es posible. También puede establecer filas y columnas para ocupar una cantidad proporcional de espacio con el [`GridUnitType.Star`](xref:Xamarin.Forms.GridUnitType.Star) valor de enumeración.

En el siguiente código XAML se muestra cómo crear un [`Grid`](xref:Xamarin.Forms.Grid) con tres filas y dos columnas:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.BasicGridPage"
             Title="Basic Grid demo">
   <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition Height="*" />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="*" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>
        ...
    </Grid>
</ContentPage>
```

En este ejemplo, [`Grid`](xref:Xamarin.Forms.Grid) tiene un alto general que es el alto de la página. `Grid`Sabe que el alto de la tercera fila es 100 unidades independientes del dispositivo. Resta ese alto de su propio alto y asigna el alto restante proporcionalmente entre la primera y la segunda fila en función del número anterior a la estrella. En este ejemplo, el alto de la primera fila es el doble de la de la segunda fila.

[`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition)Ambos objetos establecen el [`Width`](xref:Xamarin.Forms.ColumnDefinition.Width) en `*` , que es igual que `1*` , lo que significa que el ancho de la pantalla se divide equitativamente por debajo de las dos columnas.

> [!IMPORTANT]
> El valor predeterminado de la [`RowDefinition.Height`](xref:Xamarin.Forms.RowDefinition.Height) propiedad es `*` . Del mismo modo, el valor predeterminado de la [`ColumnDefinition.Width`](xref:Xamarin.Forms.ColumnDefinition.Width) propiedad es `*` . Por lo tanto, no es necesario establecer estas propiedades en los casos en los que estos valores predeterminados son aceptables.

Las vistas secundarias se pueden colocar en [`Grid`](xref:Xamarin.Forms.Grid) celdas específicas con [`Grid.Column`](xref:Xamarin.Forms.Grid.ColumnProperty) y las [`Grid.Row`](xref:Xamarin.Forms.Grid.RowProperty) propiedades adjuntas. Además, para que las vistas secundarias abarquen varias filas y columnas, use [`Grid.RowSpan`](xref:Xamarin.Forms.Grid.RowSpanProperty) y las [`Grid.ColumnSpan`](xref:Xamarin.Forms.Grid.ColumnSpanProperty) propiedades adjuntas.

En el código XAML siguiente se muestra la misma [`Grid`](xref:Xamarin.Forms.Grid) definición y también se colocan las vistas secundarias en `Grid` celdas específicas:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.BasicGridPage"
             Title="Basic Grid demo">
   <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="2*" />
            <RowDefinition />
            <RowDefinition Height="100" />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <BoxView Color="Green" />
        <Label Text="Row 0, Column 0"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Column="1"
                 Color="Blue" />
        <Label Grid.Column="1"
               Text="Row 0, Column 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Color="Teal" />
        <Label Grid.Row="1"
               Text="Row 1, Column 0"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="1"
                 Color="Purple" />
        <Label Grid.Row="1"
               Grid.Column="1"
               Text="Row1, Column 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="2"
                 Grid.ColumnSpan="2"
                 Color="Red" />
        <Label Grid.Row="2"
               Grid.ColumnSpan="2"
               Text="Row 2, Columns 0 and 1"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
    </Grid>
</ContentPage>
```

> [!NOTE]
> Las `Grid.Row` `Grid.Column` propiedades y se indizan desde 0, por lo que se `Grid.Row="2"` refiere a la tercera fila mientras se `Grid.Column="1"` hace referencia a la segunda columna. Además, ambas propiedades tienen un valor predeterminado de 0, por lo que no es necesario establecer en vistas secundarias que ocupen la primera fila o la primera columna de un [`Grid`](xref:Xamarin.Forms.Grid) .

En este ejemplo, las tres [`Grid`](xref:Xamarin.Forms.Grid) filas están ocupadas por las [`BoxView`](xref:Xamarin.Forms.BoxView) [`Label`](xref:Xamarin.Forms.Label) vistas y. La tercera fila tiene un alto de 100 unidades independientes del dispositivo, con las dos primeras filas que ocupan el espacio restante (la primera fila es dos veces más alta que la segunda fila). Las dos columnas tienen el mismo ancho y lo dividen `Grid` en la mitad. El `BoxView` de la tercera fila abarca ambas columnas.

[![Captura de pantalla de un diseño de cuadrícula básico](grid-images/basic.png "Diseño de cuadrícula básico")](grid-images/basic-large.png#lightbox "Diseño de cuadrícula básico")

Además, las vistas secundarias de una [`Grid`](xref:Xamarin.Forms.Grid) pueden compartir celdas. El orden en el que los elementos secundarios aparecen en el código XAML es el orden en el que se colocan los elementos secundarios `Grid` . En el ejemplo anterior, los [`Label`](xref:Xamarin.Forms.Label) objetos solo están visibles porque se representan en la parte superior de los [`BoxView`](xref:Xamarin.Forms.BoxView) objetos. Los `Label` objetos no estarán visibles si los `BoxView` objetos se representaban encima de ellos.

El código de C# equivalente es el siguiente:

```csharp
public class BasicGridPageCS : ContentPage
{
    public BasicGridPageCS()
    {
        Grid grid = new Grid
        {
            RowDefinitions =
            {
                new RowDefinition { Height = new GridLength(2, GridUnitType.Star) },
                new RowDefinition(),
                new RowDefinition { Height = new GridLength(100) }
            },
            ColumnDefinitions =
            {
                new ColumnDefinition(),
                new ColumnDefinition()
            }
        };

        // Row 0
        // The BoxView and Label are in row 0 and column 0, and so only needs to be added to the
        // Grid.Children collection to get default row and column settings.
        grid.Children.Add(new BoxView
        {
            Color = Color.Green
        });
        grid.Children.Add(new Label
        {
            Text = "Row 0, Column 0",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        });

        // This BoxView and Label are in row 0 and column 1, which are specified as arguments
        // to the Add method.
        grid.Children.Add(new BoxView
        {
            Color = Color.Blue
        }, 1, 0);
        grid.Children.Add(new Label
        {
            Text = "Row 0, Column 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 0);

        // Row 1
        // This BoxView and Label are in row 1 and column 0, which are specified as arguments
        // to the Add method overload.
        grid.Children.Add(new BoxView
        {
            Color = Color.Teal
        }, 0, 1, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Row 1, Column 0",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 0, 1, 1, 2); // These arguments indicate that that the child element goes in the column starting at 0 but ending before 1.
                        // They also indicate that the child element goes in the row starting at 1 but ending before 2.

        grid.Children.Add(new BoxView
        {
            Color = Color.Purple
        }, 1, 2, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Row1, Column 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 2, 1, 2);

        // Row 2
        // Alternatively, the BoxView and Label can be positioned in cells with the Grid.SetRow
        // and Grid.SetColumn methods.
        BoxView boxView = new BoxView { Color = Color.Red };
        Grid.SetRow(boxView, 2);
        Grid.SetColumnSpan(boxView, 2);
        Label label = new Label
        {
            Text = "Row 2, Column 0 and 1",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        };
        Grid.SetRow(label, 2);
        Grid.SetColumnSpan(label, 2);

        grid.Children.Add(boxView);
        grid.Children.Add(label);

        Title = "Basic Grid demo";
        Content = grid;
    }
}
```

En el código, para especificar el alto de un [`RowDefinition`](xref:Xamarin.Forms.RowDefinition) objeto y el ancho de un [`ColumnDefinition`](xref:Xamarin.Forms.ColumnDefinition) objeto, se usan valores de la [`GridLength`](xref:Xamarin.Forms.GridLength) estructura, a menudo en combinación con la [`GridUnitType`](xref:Xamarin.Forms.GridUnitType) enumeración.

El código de ejemplo anterior también muestra varios enfoques diferentes para agregar elementos secundarios a [`Grid`](xref:Xamarin.Forms.Grid) y especificar las celdas en las que residen. Cuando se usa la `Add` sobrecarga que especifica los argumentos *izquierdo*, *derecho*, *superior*e *inferior* , mientras que los argumentos *izquierdo* y *superior* siempre hacen referencia a las celdas de `Grid` , los argumentos *derecho* e *inferior* parecen hacer referencia a las celdas que están fuera de `Grid` . Esto se debe a que el argumento *derecho* siempre debe ser mayor que el argumento *izquierdo* y el argumento *inferior* siempre debe ser mayor que el argumento *superior* . En el ejemplo siguiente, en el que se supone que es 2x2 `Grid` , se muestra código equivalente mediante las dos `Add` sobrecargas:

```csharp
// left, top
grid.Children.Add(topLeft, 0, 0);           // first column, first row
grid.Children.Add(topRight, 1, 0);          // second column, first tow
grid.Children.Add(bottomLeft, 0, 1);        // first column, second row
grid.Children.Add(bottomRight, 1, 1);       // second column, second row

// left, right, top, bottom
grid.Children.Add(topLeft, 0, 1, 0, 1);     // first column, first row
grid.Children.Add(topRight, 1, 2, 0, 1);    // second column, first tow
grid.Children.Add(bottomLeft, 0, 1, 1, 2);  // first column, second row
grid.Children.Add(bottomRight, 1, 2, 1, 2); // second column, second row
```

> [!NOTE]
> Además, las vistas secundarias se pueden agregar a un [`Grid`](xref:Xamarin.Forms.Grid) con [`AddHorizontal`](xref:Xamarin.Forms.Grid.IGridList`1.AddHorizontal*) los [`AddVertical`](xref:Xamarin.Forms.Grid.IGridList`1.AddVertical*) métodos y, que agregan elementos secundarios a una sola fila o a una sola columna `Grid` . `Grid`A continuación, se expande en filas o columnas a medida que se realizan estas llamadas, así como colocar automáticamente los elementos secundarios en las celdas correctas.

## <a name="space-between-rows-and-columns"></a>Espacio entre filas y columnas

De forma predeterminada, [`Grid`](xref:Xamarin.Forms.Grid) las filas están separadas por 6 unidades de espacio independientes del dispositivo. Del mismo modo, `Grid` las columnas se separan mediante 6 unidades de espacio independientes del dispositivo. Estos valores predeterminados se pueden cambiar estableciendo [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) las [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) propiedades y, respectivamente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.GridSpacingPage"
             Title="Grid spacing demo">
    <Grid RowSpacing="0"
          ColumnSpacing="0">
        ..
    </Grid>
</ContentPage>
```

En este ejemplo se crea un [`Grid`](xref:Xamarin.Forms.Grid) que no tiene espacio entre sus filas y columnas:

[![Captura de pantalla de la cuadrícula sin espaciado entre las celdas](grid-images/spacing.png "Cuadrícula sin espaciado entre las celdas")](grid-images/spacing-large.png#lightbox "Cuadrícula sin espaciado entre las celdas")

> [!TIP]
> Las [`RowSpacing`](xref:Xamarin.Forms.Grid.RowSpacing) [`ColumnSpacing`](xref:Xamarin.Forms.Grid.ColumnSpacing) propiedades y se pueden establecer en valores negativos para que el contenido de la celda se superponga.

El código de C# equivalente es el siguiente:

```csharp
public GridSpacingPageCS()
{
    Grid grid = new Grid
    {
        RowSpacing = 0,
        ColumnSpacing = 0,
        // ...
    };
    // ...

    Content = grid;
}
```

## <a name="alignment"></a>Alineación

Las vistas secundarias de una [`Grid`](xref:Xamarin.Forms.Grid) se pueden colocar en sus celdas por las [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) propiedades y. Estas propiedades se pueden establecer en los siguientes campos del [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) struct:

- [`Start`](xref:Xamarin.Forms.LayoutOptions.Start)
- [`Center`](xref:Xamarin.Forms.LayoutOptions.Center)
- [`End`](xref:Xamarin.Forms.LayoutOptions.End)
- [`Fill`](xref:Xamarin.Forms.LayoutOptions.Fill)

> [!IMPORTANT]
> Los `AndExpands` campos del [`LayoutOptions`](xref:Xamarin.Forms.LayoutOptions) struct solo se pueden aplicar a [`StackLayout`](xref:Xamarin.Forms.StackLayout) objetos.

El código XAML siguiente crea un [`Grid`](xref:Xamarin.Forms.Grid) con nueve celdas de igual tamaño y coloca un [`Label`](xref:Xamarin.Forms.Label) en cada celda con una alineación diferente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="GridDemos.Views.GridAlignmentPage"
             Title="Grid alignment demo">
    <Grid RowSpacing="0"
          ColumnSpacing="0">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>

        <BoxView Color="AliceBlue" />
        <Label Text="Upper left"
               HorizontalOptions="Start"
               VerticalOptions="Start" />
        <BoxView Grid.Column="1"
                 Color="LightSkyBlue" />
        <Label Grid.Column="1"
               Text="Upper center"
               HorizontalOptions="Center"
               VerticalOptions="Start"/>
        <BoxView Grid.Column="2"
                 Color="CadetBlue" />
        <Label Grid.Column="2"
               Text="Upper right"
               HorizontalOptions="End"
               VerticalOptions="Start" />
        <BoxView Grid.Row="1"
                 Color="CornflowerBlue" />
        <Label Grid.Row="1"
               Text="Center left"
               HorizontalOptions="Start"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="1"
                 Color="DodgerBlue" />
        <Label Grid.Row="1"
               Grid.Column="1"
               Text="Center center"
               HorizontalOptions="Center"
               VerticalOptions="Center" />
        <BoxView Grid.Row="1"
                 Grid.Column="2"
                 Color="DarkSlateBlue" />
        <Label Grid.Row="1"
               Grid.Column="2"
               Text="Center right"
               HorizontalOptions="End"
               VerticalOptions="Center" />
        <BoxView Grid.Row="2"
                 Color="SteelBlue" />
        <Label Grid.Row="2"
               Text="Lower left"
               HorizontalOptions="Start"
               VerticalOptions="End" />
        <BoxView Grid.Row="2"
                 Grid.Column="1"
                 Color="LightBlue" />
        <Label Grid.Row="2"
               Grid.Column="1"
               Text="Lower center"
               HorizontalOptions="Center"
               VerticalOptions="End" />
        <BoxView Grid.Row="2"
                 Grid.Column="2"
                 Color="BlueViolet" />
        <Label Grid.Row="2"
               Grid.Column="2"
               Text="Lower right"
               HorizontalOptions="End"
               VerticalOptions="End" />
    </Grid>
</ContentPage>
```

En este ejemplo, los [`Label`](xref:Xamarin.Forms.Label) objetos de cada fila se alinean de manera idéntica verticalmente, pero usan diferentes alineaciones horizontales. Como alternativa, se puede considerar que los `Label` objetos de cada columna están alineados de forma idéntica horizontalmente, pero con diferentes alineaciones verticales:

[![Captura de pantalla de la alineación de celdas dentro de una cuadrícula](grid-images/alignment.png "Alineación de celdas en una cuadrícula")](grid-images/alignment-large.png#lightbox "Alineación de celdas en una cuadrícula")

El código de C# equivalente es el siguiente:

```csharp
public class GridAlignmentPageCS : ContentPage
{
    public GridAlignmentPageCS()
    {
        Grid grid = new Grid
        {
            RowSpacing = 0,
            ColumnSpacing = 0,
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition()
            },
            ColumnDefinitions =
            {
                new ColumnDefinition(),
                new ColumnDefinition(),
                new ColumnDefinition()
            }
        };

        // Row 0
        grid.Children.Add(new BoxView
        {
            Color = Color.AliceBlue
        });
        grid.Children.Add(new Label
        {
            Text = "Upper left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Start
        });

        grid.Children.Add(new BoxView
        {
            Color = Color.LightSkyBlue
        }, 1, 0);
        grid.Children.Add(new Label
        {
            Text = "Upper center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Start
        }, 1, 0);

        grid.Children.Add(new BoxView
        {
            Color = Color.CadetBlue
        }, 2, 0);
        grid.Children.Add(new Label
        {
            Text = "Upper right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.Start
        }, 2, 0);

        // Row 1
        grid.Children.Add(new BoxView
        {
            Color = Color.CornflowerBlue
        }, 0, 1);
        grid.Children.Add(new Label
        {
            Text = "Center left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.Center
        }, 0, 1);

        grid.Children.Add(new BoxView
        {
            Color = Color.DodgerBlue
        }, 1, 1);
        grid.Children.Add(new Label
        {
            Text = "Center center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.Center
        }, 1, 1);

        grid.Children.Add(new BoxView
        {
            Color = Color.DarkSlateBlue
        }, 2, 1);
        grid.Children.Add(new Label
        {
            Text = "Center right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.Center
        }, 2, 1);

        // Row 2
        grid.Children.Add(new BoxView
        {
            Color = Color.SteelBlue
        }, 0, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower left",
            HorizontalOptions = LayoutOptions.Start,
            VerticalOptions = LayoutOptions.End
        }, 0, 2);

        grid.Children.Add(new BoxView
        {
            Color = Color.LightBlue
        }, 1, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower center",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.End
        }, 1, 2);

        grid.Children.Add(new BoxView
        {
            Color = Color.BlueViolet
        }, 2, 2);
        grid.Children.Add(new Label
        {
            Text = "Lower right",
            HorizontalOptions = LayoutOptions.End,
            VerticalOptions = LayoutOptions.End
        }, 2, 2);

        Title = "Grid alignment demo";
        Content = grid;
    }
}
```

## <a name="nested-grid-objects"></a>Objetos de cuadrícula anidados

[`Grid`](xref:Xamarin.Forms.Grid)Se puede utilizar como un diseño primario que contiene objetos secundarios anidados `Grid` u otros diseños secundarios. Al anidar `Grid` objetos,, `Grid.Row` , `Grid.Column` `Grid.RowSpan` y `Grid.ColumnSpan` las propiedades adjuntas siempre hacen referencia a la posición de las vistas dentro de su elemento primario `Grid` .

En el código XAML siguiente se muestra un ejemplo de anidamiento de [`Grid`](xref:Xamarin.Forms.Grid) objetos:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:converters="clr-namespace:GridDemos.Converters"
             x:Class="GridDemos.Views.ColorSlidersGridPage"
             Title="Nested Grids demo">

    <ContentPage.Resources>
        <converters:DoubleToIntConverter x:Key="doubleToInt" />

        <Style TargetType="Label">
            <Setter Property="HorizontalTextAlignment"
                    Value="Center" />
        </Style>
    </ContentPage.Resources>

    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <BoxView x:Name="boxView"
                 Color="Black" />
        <Grid Grid.Row="1"
              Margin="20">
            <Grid.RowDefinitions>
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
                <RowDefinition />
            </Grid.RowDefinitions>
            <Slider x:Name="redSlider"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="1"
                   Text="{Binding Source={x:Reference redSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Red = {0}'}" />
            <Slider x:Name="greenSlider"
                    Grid.Row="2"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="3"
                   Text="{Binding Source={x:Reference greenSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Green = {0}'}" />
            <Slider x:Name="blueSlider"
                    Grid.Row="4"
                    ValueChanged="OnSliderValueChanged" />
            <Label Grid.Row="5"
                   Text="{Binding Source={x:Reference blueSlider},
                                  Path=Value,
                                  Converter={StaticResource doubleToInt},
                                  ConverterParameter=255,
                                  StringFormat='Blue = {0}'}" />
        </Grid>
    </Grid>
</ContentPage>
```

En este ejemplo, el [`Grid`](xref:Xamarin.Forms.Grid) diseño raíz contiene un [`BoxView`](xref:Xamarin.Forms.BoxView) en su primera fila y un elemento secundario `Grid` en la segunda fila. El elemento secundario `Grid` contiene [`Slider`](xref:Xamarin.Forms.Slider) objetos que manipulan el color que muestra `BoxView` , y los [`Label`](xref:Xamarin.Forms.Label) objetos que muestran el valor de cada uno de ellos `Slider` :

[![Captura de pantalla de cuadrículas anidadas](grid-images/nesting.png "Objetos de cuadrícula anidados")](grid-images/nesting-large.png#lightbox "Objetos de cuadrícula anidados")

> [!IMPORTANT]
> Cuanto más profundo sea el anidamiento de [`Grid`](xref:Xamarin.Forms.Grid) objetos y otros diseños, más los diseños anidados afectarán al rendimiento. Para obtener más información, vea [elegir el diseño correcto](~/xamarin-forms/deploy-test/performance.md#choose-the-correct-layout).

El código de C# equivalente es el siguiente:

```csharp
public class ColorSlidersGridPageCS : ContentPage
{
    BoxView boxView;
    Slider redSlider;
    Slider greenSlider;
    Slider blueSlider;

    public ColorSlidersGridPageCS()
    {
        // Create an implicit style for the Labels
        Style labelStyle = new Style(typeof(Label))
        {
            Setters =
            {
                new Setter { Property = Label.HorizontalTextAlignmentProperty, Value = TextAlignment.Center }
            }
        };
        Resources.Add(labelStyle);

        // Root page layout
        Grid rootGrid = new Grid
        {
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition()
            }
        };

        boxView = new BoxView { Color = Color.Black };
        rootGrid.Children.Add(boxView);

        // Child page layout
        Grid childGrid = new Grid
        {
            Margin = new Thickness(20),
            RowDefinitions =
            {
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition(),
                new RowDefinition()
            }
        };

        DoubleToIntConverter doubleToInt = new DoubleToIntConverter();

        redSlider = new Slider();
        redSlider.ValueChanged += OnSliderValueChanged;
        childGrid.Children.Add(redSlider);

        Label redLabel = new Label();
        redLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Red = {0}", source: redSlider));
        Grid.SetRow(redLabel, 1);
        childGrid.Children.Add(redLabel);

        greenSlider = new Slider();
        greenSlider.ValueChanged += OnSliderValueChanged;
        Grid.SetRow(greenSlider, 2);
        childGrid.Children.Add(greenSlider);

        Label greenLabel = new Label();
        greenLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Green = {0}", source: greenSlider));
        Grid.SetRow(greenLabel, 3);
        childGrid.Children.Add(greenLabel);

        blueSlider = new Slider();
        blueSlider.ValueChanged += OnSliderValueChanged;
        Grid.SetRow(blueSlider, 4);
        childGrid.Children.Add(blueSlider);

        Label blueLabel = new Label();
        blueLabel.SetBinding(Label.TextProperty, new Binding("Value", converter: doubleToInt, converterParameter: "255", stringFormat: "Blue = {0}", source: blueSlider));
        Grid.SetRow(blueLabel, 5);
        childGrid.Children.Add(blueLabel);

        // Place the child Grid in the root Grid
        rootGrid.Children.Add(childGrid, 0, 1);

        Title = "Nested Grids demo";
        Content = rootGrid;
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs e)
    {
        boxView.Color = new Color(redSlider.Value, greenSlider.Value, blueSlider.Value);
    }
}
```

## <a name="related-links"></a>Vínculos relacionados

- [Demostraciones de cuadrícula (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-griddemos)
- [Opciones de diseño de Xamarin. Forms](layout-options.md)
- [Elegir un diseño de Xamarin. Forms](choose-layout.md)
- [Mejora del rendimiento de aplicaciones Xamarin.Forms](~/xamarin-forms/deploy-test/performance.md)
