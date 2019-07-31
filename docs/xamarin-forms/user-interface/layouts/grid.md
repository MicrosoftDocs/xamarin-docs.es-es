---
title: Cuadrícula de Xamarin.Forms
description: En este artículo se explica cómo usar la clase de la cuadrícula de Xamarin.Forms para presentar vistas en las cuadrículas, que poseen las filas y columnas.
ms.prod: xamarin
ms.assetid: 762B1802-D185-494C-B643-74EED55882FE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2017
ms.openlocfilehash: 38206812fbd746a63c5bff18111779c25deedc66
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648533"
---
# <a name="xamarinforms-grid"></a>Cuadrícula de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

[`Grid`](xref:Xamarin.Forms.Grid) permite organizar las vistas en filas y columnas. Las filas y columnas se pueden establecer para tener tamaños proporcionales o tamaños absolutos. El `Grid` diseño no debe confundirse con las tablas tradicionales y no está diseñada para presentar los datos tabulares. `Grid` no tiene el concepto de fila, columna o formato de celdas. A diferencia de las tablas HTML, `Grid` está pensado únicamente para diseño de contenido.

[![](grid-images/layouts-sml.png "Los diseños de Xamarin.Forms")](grid-images/layouts.png#lightbox "los diseños de Xamarin.Forms")

Este artículo se trata:

- **[Propósito](#purpose)**  &ndash; usos comunes de `Grid`.
- **[Uso](#usage)**  &ndash; cómo usar `Grid` para lograr el diseño deseado.
  - **[Las filas y columnas](#rows-and-columns)**  &ndash; especificar las filas y columnas para el `Grid`.
  - **[Colocación de las vistas](#placing-views-in-a-grid)**  &ndash; agregar vistas a la cuadrícula de columnas y filas específicas.
  - **[Espaciado](#spacing)**  &ndash; configurar los espacios entre las filas y columnas.
  - **[Intervalos](#spans)**  &ndash; configurar elementos que se va a abarcar varias filas o columnas.

![](grid-images/grid.png "Exploración de la cuadrícula")

## <a name="purpose"></a>Propósito

`Grid` puede usarse para organizar las vistas en una cuadrícula. Esto es útil en un número de casos:

- Organizar botones en una aplicación de calculadora
- Organizar botones y alternativas en una cuadrícula, como iOS o Android pantallas de inicio
- Organizar las vistas para que sean del mismo tamaño en una dimensión (como en algunas barras de herramientas)

## <a name="usage"></a>Uso

A diferencia de las tablas tradicionales, `Grid` no infiere el número y tamaño de filas y columnas del contenido. En su lugar, `Grid` tiene `RowDefinitions` y `ColumnDefinitions` colecciones. Éstos contienen las definiciones de dispuestos cuántas filas y columnas. Las vistas se agregan a `Grid` con la fila especificada y los índices de columnas, que identifican qué fila y columna que se debe colocar en una vista.

### <a name="rows-and-columns"></a>Las filas y columnas

Información de fila y columna se almacena en `Grid`del `RowDefinitions`  &  `ColumnDefinitions` propiedades, que son colecciones de cada de [ `RowDefinition` ](xref:Xamarin.Forms.RowDefinition) y [ `ColumnDefinition` ](xref:Xamarin.Forms.ColumnDefinition)objetos, respectivamente. `RowDefinition` tiene una propiedad única, `Height`, y `ColumnDefinition` tiene una propiedad única, `Width`. Las opciones de alto y ancho son los siguientes:

- **Auto** &ndash; automáticamente los tamaños para ajustar el contenido de la fila o columna. Especificado como [ `GridUnitType.Auto` ](xref:Xamarin.Forms.GridUnitType) en C# o como `Auto` en XAML.
- **Proportional(*)** &ndash; cambia el tamaño de filas y columnas como una proporción del espacio restante. Especificado como un valor y `GridUnitType.Star` en C# y como `#*` en XAML, con `#` que el valor deseado. Especificación de una fila o columna con `*` hará que se va a rellenar el espacio disponible.
- **Absoluta** &ndash; tamaños de las columnas y filas con valores específicos de alto y ancho fijos. Especificado como un valor y `GridUnitType.Absolute` en C# y como `#` en XAML, con `#` que el valor deseado.

> [!NOTE]
> Los valores de ancho de las columnas se `*` establecen de forma predeterminada en Xamarin. Forms, lo que garantiza que la columna rellenará el espacio disponible. Los valores de alto de las filas también se `*` establecen de forma predeterminada.

Considere la posibilidad de una aplicación que necesita tres filas y dos columnas. La fila inferior debe ser exactamente 200px alto y la fila superior debe ser dos veces más alto que la fila central. La columna izquierda debe ser lo suficientemente ancho como para que quepa el contenido y la columna derecha debe rellenar el espacio restante.

En XAML:

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="2*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="200" />
  </Grid.RowDefinitions>
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="Auto" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
</Grid>
```

En C#:

```csharp
Grid grid = new Grid();
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength(2, GridUnitType.Star) });
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength(200)});
grid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (200) });
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Auto) });
```

### <a name="placing-views-in-a-grid"></a>Colocación de las vistas en una cuadrícula

Para colocar las vistas en un `Grid` , deberá agregarlos como elementos secundarios a la cuadrícula y, después, especifique qué fila y columna pertenecen en.

En XAML, utilice `Grid.Row` y `Grid.Column` en cada vista individual para especificar la selección de ubicación. Tenga en cuenta que `Grid.Row` y `Grid.Column` especificar ubicación basándose en las listas de base cero de filas y columnas. Esto significa que en una cuadrícula 4 x 4, la celda superior izquierda es (0,0) y la celda inferior derecha es (3,3).

El `Grid` se muestra a continuación contiene cuatro celdas:

![](grid-images/label-grid.png "Cuadrícula de cuatro vistas")

En XAML:

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
  </Grid.RowDefinitions>
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
  <Label Text="Top Left" Grid.Row="0" Grid.Column="0" />
  <Label Text="Top Right" Grid.Row="0" Grid.Column="1" />
  <Label Text="Bottom Left" Grid.Row="1" Grid.Column="0" />
  <Label Text="Bottom Right" Grid.Row="1" Grid.Column="1" />
</Grid>
```

En C#:

```csharp
var grid = new Grid();

grid.RowDefinitions.Add(new RowDefinition { Height = new GridLength(1, GridUnitType.Star)});
grid.RowDefinitions.Add(new RowDefinition { Height = new GridLength(1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Star)});

var topLeft = new Label { Text = "Top Left" };
var topRight = new Label { Text = "Top Right" };
var bottomLeft = new Label { Text = "Bottom Left" };
var bottomRight = new Label { Text = "Bottom Right" };

grid.Children.Add(topLeft, 0, 0);
grid.Children.Add(topRight, 1, 0);
grid.Children.Add(bottomLeft, 0, 1);
grid.Children.Add(bottomRight, 1, 1);
```

El código anterior crea la cuadrícula con cuatro etiquetas, dos columnas y dos filas. Tenga en cuenta que cada etiqueta tendrá el mismo tamaño y que las filas se expandirán para usar todo el espacio disponible.

En el ejemplo anterior, las vistas se agregan a la [ `Grid.Children` ](xref:Xamarin.Forms.Grid.Children) colección utilizando el [ `Add` ](xref:Xamarin.Forms.Grid.IGridList`1.Add*) sobrecarga que especifique los argumentos izquierdo y superiores. Cuando se usa el [ `Add` ](xref:Xamarin.Forms.Grid.IGridList`1.Add*) sobrecarga que especifique a la izquierda, derecha, superior y argumentos de la parte inferior, mientras la izquierda y argumentos superiores siempre hará referencia a las celdas de la [ `Grid` ](xref:Xamarin.Forms.Grid), la derecha y argumentos de la parte inferior pueden parecer que hacen referencia a las celdas que están fuera de la `Grid`. Esto es porque el argumento derecho siempre debe ser mayor que el argumento izquierdo y el argumento final siempre debe ser mayor que el argumento superior. En el ejemplo siguiente se muestra código equivalente que utiliza ambos `Add` sobrecargas:

```csharp
// left, top
grid.Children.Add(topLeft, 0, 0);
grid.Children.Add(topRight, 1, 0);
grid.Children.Add(bottomLeft, 0, 1);
grid.Children.Add(bottomRight, 1, 1);

// left, right, top, bottom
grid.Children.Add(topLeft, 0, 1, 0, 1);
grid.Children.Add(topRight, 1, 2, 0, 1);
grid.Children.Add(bottomLeft, 0, 1, 1, 2);
grid.Children.Add(bottomRight, 1, 2, 1, 2);
```

### <a name="spacing"></a>Espaciado

`Grid` tiene propiedades para controlar el espaciado entre las filas y columnas. Las propiedades siguientes están disponibles para personalizar el `Grid`:

- **ColumnSpacing** &ndash; la cantidad de espacio entre las columnas. El valor predeterminado de esta propiedad es 6.
- **RowSpacing** &ndash; la cantidad de espacio entre las filas. El valor predeterminado de esta propiedad es 6.

El XAML siguiente especifica un `Grid` con dos columnas, una fila y 5 px de espaciado entre columnas:

```xaml
<Grid ColumnSpacing="5">
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
</Grid>
```

En C#:

```csharp
var grid = new Grid { ColumnSpacing = 5 };
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star)});
```

### <a name="spans"></a>Intervalos

A menudo cuando se trabaja con una cuadrícula, hay un elemento que debe ocupar más de una fila o columna. Considere la posibilidad de una aplicación de calculadora simple:

![](grid-images/calculator.png "Aplicación Calulator")

Tenga en cuenta que el botón 0 abarca dos columnas, al igual que las calculadoras integradas para cada plataforma. Esto se logra mediante el `ColumnSpan` propiedad, que especifica cuántas columnas un elemento deben ocupar. El XAML para ese botón:

```xaml
<Button Text = "0" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" />
```

Y en C#:

```csharp
Button zeroButton = new Button { Text = "0" };
controlGrid.Children.Add (zeroButton, 0, 4);
Grid.SetColumnSpan (zeroButton, 2);
```

Tenga en cuenta que en el código, los métodos estáticos de la `Grid` clase se utilizan para realizar cambios de posicionamiento, incluidos los cambios a `ColumnSpan` y `RowSpan`. Tenga en cuenta que, con otras propiedades que se pueden establecer en cualquier momento, a diferencia de las propiedades establecidas mediante los métodos estáticos en ya deben puede estar también en la cuadrícula antes de modificarla.

El XAML completo para la aplicación de calculadora anterior es como sigue:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.CalculatorGridXAML"
Title = "Calculator - XAML"
BackgroundColor="#404040">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="plainButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#eee"/>
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
            <Style x:Key="darkerButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#ddd"/>
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
            <Style x:Key="orangeButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#E8AD00"/>
                <Setter Property="TextColor" Value="White" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <Grid x:Name="controlGrid" RowSpacing="1" ColumnSpacing="1">
            <Grid.RowDefinitions>
                <RowDefinition Height="150" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
            <Label Text="0" Grid.Row="0" HorizontalTextAlignment="End" VerticalTextAlignment="End" TextColor="White"
        FontSize="60" Grid.ColumnSpan="4" />
            <Button Text = "C" Grid.Row="1" Grid.Column="0"
        Style="{StaticResource darkerButton}" />
            <Button Text = "+/-" Grid.Row="1" Grid.Column="1"
        Style="{StaticResource darkerButton}" />
            <Button Text = "%" Grid.Row="1" Grid.Column="2"
        Style="{StaticResource darkerButton}" />
            <Button Text = "div" Grid.Row="1" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "7" Grid.Row="2" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "8" Grid.Row="2" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "9" Grid.Row="2" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "X" Grid.Row="2" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "4" Grid.Row="3" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "5" Grid.Row="3" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "6" Grid.Row="3" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "-" Grid.Row="3" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "1" Grid.Row="4" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "2" Grid.Row="4" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "3" Grid.Row="4" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "+" Grid.Row="4" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "0" Grid.ColumnSpan="2"
        Grid.Row="5" Grid.Column="0" Style="{StaticResource plainButton}" />
            <Button Text = "." Grid.Row="5" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "=" Grid.Row="5" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

Observe que tanto la etiqueta en la parte superior de la cuadrícula y el botón cero occuping más de una columna. Aunque se podría lograr un diseño similar con cuadrículas anidadas, la `ColumnSpan`  &  `RowSpan` enfoque es más sencillo.

La implementación de C#:

```csharp
public CalculatorGridCode ()
{
  Title = "Calculator - C#";
  BackgroundColor = Color.FromHex ("#404040");

  var plainButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#eee") },
      new Setter { Property = Button.TextColorProperty, Value = Color.Black },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };
  var darkerButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#ddd") },
      new Setter { Property = Button.TextColorProperty, Value = Color.Black },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };
  var orangeButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#E8AD00") },
      new Setter { Property = Button.TextColorProperty, Value = Color.White },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };

  var controlGrid = new Grid { RowSpacing = 1, ColumnSpacing = 1 };
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (150) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });

  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });

  var label = new Label {
    Text = "0",
    HorizontalTextAlignment = TextAlignment.End,
    VerticalTextAlignment = TextAlignment.End,
    TextColor = Color.White,
    FontSize = 60
  };
  controlGrid.Children.Add (label, 0, 0);

  Grid.SetColumnSpan (label, 4);

  controlGrid.Children.Add (new Button { Text = "C", Style = darkerButton }, 0, 1);
  controlGrid.Children.Add (new Button { Text = "+/-", Style = darkerButton }, 1, 1);
  controlGrid.Children.Add (new Button { Text = "%", Style = darkerButton }, 2, 1);
  controlGrid.Children.Add (new Button { Text = "div", Style = orangeButton }, 3, 1);
  controlGrid.Children.Add (new Button { Text = "7", Style = plainButton }, 0, 2);
  controlGrid.Children.Add (new Button { Text = "8", Style = plainButton }, 1, 2);
  controlGrid.Children.Add (new Button { Text = "9", Style = plainButton }, 2, 2);
  controlGrid.Children.Add (new Button { Text = "X", Style = orangeButton }, 3, 2);
  controlGrid.Children.Add (new Button { Text = "4", Style = plainButton }, 0, 3);
  controlGrid.Children.Add (new Button { Text = "5", Style = plainButton }, 1, 3);
  controlGrid.Children.Add (new Button { Text = "6", Style = plainButton }, 2, 3);
  controlGrid.Children.Add (new Button { Text = "-", Style = orangeButton }, 3, 3);
  controlGrid.Children.Add (new Button { Text = "1", Style = plainButton }, 0, 4);
  controlGrid.Children.Add (new Button { Text = "2", Style = plainButton }, 1, 4);
  controlGrid.Children.Add (new Button { Text = "3", Style = plainButton }, 2, 4);
  controlGrid.Children.Add (new Button { Text = "+", Style = orangeButton }, 3, 4);
  controlGrid.Children.Add (new Button { Text = ".", Style = plainButton }, 2, 5);
  controlGrid.Children.Add (new Button { Text = "=", Style = orangeButton }, 3, 5);

  var zeroButton = new Button { Text = "0", Style = plainButton };
  controlGrid.Children.Add (zeroButton, 0, 5);
  Grid.SetColumnSpan (zeroButton, 2);

  Content = controlGrid;
}
```


## <a name="related-links"></a>Vínculos relacionados

- [Creación de aplicaciones móviles con Xamarin.Forms, capítulo 17](https://developer.xamarin.com/r/xamarin-forms/book/chapter17.pdf)
- [Grid](xref:Xamarin.Forms.Grid)
- [Diseño (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Ejemplo de BusinessTumble (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
