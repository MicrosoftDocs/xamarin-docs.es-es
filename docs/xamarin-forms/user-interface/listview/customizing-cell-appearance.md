---
title: Personalizar la apariencia de una celda de ListView
description: Este artículo explora las opciones para presentar datos en las aplicaciones de Xamarin.Forms, al tiempo que aprovecha la comodidad del control ListView.
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/12/2019
ms.openlocfilehash: ab54b54c9f2f7d6d7748137ea079439b7c3ddfca
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79306540"
---
# <a name="customizing-listview-cell-appearance"></a>Personalizar la apariencia de una celda de ListView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

La clase de [`ListView`](xref:Xamarin.Forms.ListView) de Xamarin. Forms se usa para presentar listas desplazables, que se pueden personalizar mediante el uso de elementos de `ViewCell`. Un elemento `ViewCell` puede mostrar texto e imágenes, indicar un estado verdadero/falso y recibir datos proporcionados por el usuario.

## <a name="built-in-cells"></a>Integrado en celdas
Xamarin. Forms incluye celdas integradas que funcionan para muchas aplicaciones:

- [`TextCell`](#textcell) controles se usan para mostrar texto con una segunda línea opcional para el texto detallado.
- [`ImageCell`](#imagecell) controles son similares a `TextCell`s, pero incluyen una imagen a la izquierda del texto.
- los controles de `SwitchCell` se usan para presentar y capturar Estados de ON/OFF o true/false.
- `EntryCell` controles se usan para presentar datos de texto que el usuario puede editar.

Los controles [`SwitchCell`](~/xamarin-forms/user-interface/tableview.md#switchcell) y [`EntryCell`](~/xamarin-forms/user-interface/tableview.md#entrycell) se utilizan con más frecuencia en el contexto de un [`TableView`](~/xamarin-forms/user-interface/tableview.md).

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell) es una celda para mostrar texto, opcionalmente con una segunda línea como texto detallado. En la captura de pantalla siguiente se muestra `TextCell` elementos en iOS y Android:

![](customizing-cell-appearance-images/text-cell-default.png "Default TextCell Example")

TextCells se representan como controles nativos en tiempo de ejecución, por lo que el rendimiento es muy bueno en comparación con un `ViewCell`personalizado. Los TextCells son personalizables, lo que le permite establecer las siguientes propiedades:

- `Text` &ndash; el texto que se muestra en la primera línea, en una fuente grande.
- `Detail` &ndash; el texto que se muestra debajo de la primera línea, en una fuente menor.
- `TextColor` &ndash; el color del texto.
- `DetailColor` &ndash; el color del texto detallado

En la captura de pantalla siguiente se muestra `TextCell` elementos con propiedades de color personalizadas:

![](customizing-cell-appearance-images/text-cell-custom.png "Custom TextCell Example")

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell), como `TextCell`, se puede usar para mostrar texto y texto de detalle secundario, y ofrece un gran rendimiento mediante el uso de los controles nativos de cada plataforma. `ImageCell` difiere de `TextCell` en que muestra una imagen a la izquierda del texto.

En la captura de pantalla siguiente se muestra `ImageCell` elementos de iOS y Android: !["default ImageCell example"](customizing-cell-appearance-images/image-cell-default.png "Ejemplo de ImageCell predeterminado")

`ImageCell` resulta útil cuando es necesario mostrar una lista de datos con un aspecto visual, como una lista de contactos o películas. `ImageCell`s son personalizables, lo que le permite establecer:

- `Text` &ndash; el texto que se muestra en la primera línea, en una fuente grande
- `Detail` &ndash; el texto que se muestra debajo de la primera línea, en una fuente más pequeña
- `TextColor` &ndash; el color del texto
- `DetailColor` &ndash; el color del texto detallado
- `ImageSource` &ndash; la imagen que se va a mostrar junto al texto

En la captura de pantalla siguiente se muestra `ImageCell` elementos con propiedades de color personalizadas: !["ejemplo de ImageCell personalizado"](customizing-cell-appearance-images/image-cell-custom.png "Ejemplo de ImageCell personalizado")

## <a name="custom-cells"></a>Celdas personalizadas
Las celdas personalizadas permiten crear diseños de celda que no son compatibles con las celdas integradas. Por ejemplo, desea presentar una celda con dos etiquetas que tengan el mismo peso. Un `TextCell` sería insuficiente porque el `TextCell` tiene una etiqueta que es menor. La mayoría de las personalizaciones de celda agregan datos adicionales de solo lectura (por ejemplo, etiquetas adicionales, imágenes u otra información de presentación).

Todas las celdas personalizadas se deben derivar de [`ViewCell`](xref:Xamarin.Forms.ViewCell), la misma clase base que usan todos los tipos de celdas integrados.

Xamarin. Forms ofrece un [comportamiento de almacenamiento en caché](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy) en el control `ListView` que puede mejorar el rendimiento del desplazamiento para algunos tipos de celdas personalizadas.

En la captura de pantalla siguiente se muestra un ejemplo de una celda personalizada:

!["Ejemplo de celda personalizada"](customizing-cell-appearance-images/custom-cell.png "Ejemplo de celda personalizada")

### <a name="xaml"></a>XAML
La celda personalizada que se muestra en la captura de pantalla anterior se puede crear con el código XAML siguiente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="demoListView.ImageCellPage">
    <ContentPage.Content>
        <ListView  x:Name="listView">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell>
                        <StackLayout BackgroundColor="#eee"
                        Orientation="Vertical">
                            <StackLayout Orientation="Horizontal">
                                <Image Source="{Binding image}" />
                                <Label Text="{Binding title}"
                                TextColor="#f35e20" />
                                <Label Text="{Binding subtitle}"
                                HorizontalOptions="EndAndExpand"
                                TextColor="#503026" />
                            </StackLayout>
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

El código XAML funciona de la siguiente manera:

- La celda personalizada está anidada dentro de un `DataTemplate`, que se encuentra dentro de `ListView.ItemTemplate`. Este es el mismo proceso que el uso de cualquier celda integrada.
- `ViewCell` es el tipo de la celda personalizada. El elemento secundario del elemento `DataTemplate` debe ser o derivar de la clase `ViewCell`.
- Dentro del `ViewCell`, el diseño se puede administrar con cualquier diseño de Xamarin. Forms. En este ejemplo, el diseño se administra mediante una `StackLayout`, lo que permite personalizar el color de fondo.

> [!NOTE]
> Cualquier propiedad de `StackLayout` que se pueda enlazar se puede enlazar dentro de una celda personalizada. Sin embargo, esta funcionalidad no se muestra en el ejemplo de XAML.

### <a name="code"></a>Código

También se puede crear una celda personalizada en el código. En primer lugar, se debe crear una clase personalizada que derive de `ViewCell`:

```csharp
public class CustomCell : ViewCell
    {
        public CustomCell()
        {
            //instantiate each of our views
            var image = new Image ();
            StackLayout cellWrapper = new StackLayout ();
            StackLayout horizontalLayout = new StackLayout ();
            Label left = new Label ();
            Label right = new Label ();

            //set bindings
            left.SetBinding (Label.TextProperty, "title");
            right.SetBinding (Label.TextProperty, "subtitle");
            image.SetBinding (Image.SourceProperty, "image");

            //Set properties for desired design
            cellWrapper.BackgroundColor = Color.FromHex ("#eee");
            horizontalLayout.Orientation = StackOrientation.Horizontal;
            right.HorizontalOptions = LayoutOptions.EndAndExpand;
            left.TextColor = Color.FromHex ("#f35e20");
            right.TextColor = Color.FromHex ("503026");

            //add views to the view hierarchy
            horizontalLayout.Children.Add (image);
            horizontalLayout.Children.Add (left);
            horizontalLayout.Children.Add (right);
            cellWrapper.Children.Add (horizontalLayout);
            View = cellWrapper;
        }
    }
```

En el constructor de la página, la propiedad `ItemTemplate` de ListView está establecida en un `DataTemplate` con el tipo de `CustomCell` especificado:

```csharp
public partial class ImageCellPage : ContentPage
    {
        public ImageCellPage ()
        {
            InitializeComponent ();
            listView.ItemTemplate = new DataTemplate (typeof(CustomCell));
        }
    }
```

### <a name="binding-context-changes"></a>Cambios de contexto de enlace

Al enlazar a las instancias de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) de un tipo de celda personalizado, los controles de interfaz de usuario que muestran los valores de `BindableProperty` deben utilizar el [`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged) invalidar para establecer los datos que se van a mostrar en cada celda, en lugar del constructor de celda, como se muestra en el ejemplo de código siguiente:

```csharp
public class CustomCell : ViewCell
{
    Label nameLabel, ageLabel, locationLabel;

    public static readonly BindableProperty NameProperty =
        BindableProperty.Create ("Name", typeof(string), typeof(CustomCell), "Name");
    public static readonly BindableProperty AgeProperty =
        BindableProperty.Create ("Age", typeof(int), typeof(CustomCell), 0);
    public static readonly BindableProperty LocationProperty =
        BindableProperty.Create ("Location", typeof(string), typeof(CustomCell), "Location");

    public string Name
    {
        get { return(string)GetValue (NameProperty); }
        set { SetValue (NameProperty, value); }
    }

    public int Age
    {
        get { return(int)GetValue (AgeProperty); }
        set { SetValue (AgeProperty, value); }
    }

    public string Location
    {
        get { return(string)GetValue (LocationProperty); }
        set { SetValue (LocationProperty, value); }
    }
    ...

    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();

        if (BindingContext != null)
        {
            nameLabel.Text = Name;
            ageLabel.Text = Age.ToString ();
            locationLabel.Text = Location;
        }
    }
}
```

Se llamará al reemplazo [`OnBindingContextChanged`](xref:Xamarin.Forms.Cell.OnBindingContextChanged) cuando se desencadene el evento de [`BindingContextChanged`](xref:Xamarin.Forms.BindableObject.BindingContextChanged) , en respuesta al valor de la propiedad [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) que cambia. Por lo tanto, cuando el `BindingContext` cambia, los controles de interfaz de usuario que muestran los valores de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) deben establecer sus datos. Tenga en cuenta que se debe comprobar si hay un valor `null` en el `BindingContext`, ya que este se puede establecer mediante Xamarin. Forms para la recolección de elementos no utilizados, lo que, a su vez, dará lugar a la invalidación de `OnBindingContextChanged`.

Como alternativa, los controles de interfaz de usuario se pueden enlazar a las instancias de [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) para mostrar sus valores, lo que elimina la necesidad de invalidar el método `OnBindingContextChanged`.

> [!NOTE]
> Al reemplazar `OnBindingContextChanged`, asegúrese de que se llama al método `OnBindingContextChanged` de la clase base para que los delegados registrados reciban el evento `BindingContextChanged`.

En XAML, el tipo de celda personalizado de enlace a datos puede lograrse tal como se muestra en el ejemplo de código siguiente:

```xaml
<ListView x:Name="listView">
    <ListView.ItemTemplate>
        <DataTemplate>
            <local:CustomCell Name="{Binding Name}" Age="{Binding Age}" Location="{Binding Location}" />
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

Esto enlaza las propiedades `Name`, `Age`y `Location` enlazables de la instancia de `CustomCell` a las propiedades `Name`, `Age`y `Location` de cada objeto de la colección subyacente.

El enlace equivalente en C# se muestra en el ejemplo de código siguiente:

```csharp
var customCell = new DataTemplate (typeof(CustomCell));
customCell.SetBinding (CustomCell.NameProperty, "Name");
customCell.SetBinding (CustomCell.AgeProperty, "Age");
customCell.SetBinding (CustomCell.LocationProperty, "Location");

var listView = new ListView
{
    ItemsSource = people,
    ItemTemplate = customCell
};
```

En iOS y Android, si el [`ListView`](xref:Xamarin.Forms.ListView) está reciclando elementos y la celda personalizada usa un representador personalizado, el representador personalizado debe implementar correctamente la notificación de cambio de propiedad. Cuando se reutilizan las celdas, sus valores de propiedad cambiarán cuando el contexto de enlace se actualice al de una celda disponible, con `PropertyChanged` eventos que se produzcan. Para obtener más información, consulte [Personalización de un ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md). Para obtener más información sobre el reciclaje de celdas, vea [estrategia de almacenamiento en caché](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy).

## <a name="related-links"></a>Vínculos relacionados

- [Celdas integradas (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Celdas personalizadas (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Contexto de enlace cambiado (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)
