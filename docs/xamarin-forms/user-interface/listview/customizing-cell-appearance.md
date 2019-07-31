---
title: Personalizar la apariencia de una celda de ListView
description: Este artículo explora las opciones para presentar datos en las aplicaciones de Xamarin.Forms, al tiempo que aprovecha la comodidad del control ListView.
ms.prod: xamarin
ms.assetid: FD45CB91-1A8F-46FB-B432-6BC20492E456
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/07/2016
ms.openlocfilehash: ecb9e84473778f5185276a854bfbf2fdfcbb6528
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68654844"
---
# <a name="customizing-listview-cell-appearance"></a>Personalizar la apariencia de una celda de ListView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

[`ListView`](xref:Xamarin.Forms.ListView)presenta listas desplazables, que se pueden personalizar mediante el uso `ViewCell`de s. `ViewCells` puede usarse para mostrar texto e imágenes, que indica un estado verdadero/falso y recibir datos de entrada del usuario.

<a name="Built_in_Cells" />

## <a name="built-in-cells"></a>Integrado en celdas
Xamarin.Forms incluye celdas integradas que funcionan para muchas aplicaciones simples:

- **TextCell** &ndash; para mostrar texto
- **ImageCell** &ndash; para mostrar una imagen con texto.

Las otras dos celdas [ `SwitchCell` ](~/xamarin-forms/user-interface/tableview.md#switchcell) y [ `EntryCell` ](~/xamarin-forms/user-interface/tableview.md#entrycell) están disponibles, pero no se usan habitualmente con `ListView`. Consulte [ `TableView` ](~/xamarin-forms/user-interface/tableview.md) para obtener más información acerca de estas celdas.

<a name="TextCell" />

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell) es una celda para mostrar texto, opcionalmente con una segunda línea como texto de detalle.

TextCells se representan como controles nativos en tiempo de ejecución, por lo que es muy buen rendimiento en comparación con una personalizada `ViewCell`. TextCells son personalizables, lo que permite establecer:

- `Text` &ndash; el texto que se muestra en la primera línea, en el tamaño de fuente.
- `Detail` &ndash; el texto que se muestra debajo de la primera línea, en una fuente menor.
- `TextColor` &ndash; el color del texto.
- `DetailColor` &ndash; el color del texto de detalle

![](customizing-cell-appearance-images/text-cell-default.png "Ejemplo de TextCell predeterminado")

![](customizing-cell-appearance-images/text-cell-custom.png "Ejemplo de TextCell personalizada")

<a name="ImageCell" />

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell), como `TextCell`, se puede usar para mostrar texto y texto de detalles secundario y ofrece un excelente rendimiento mediante el uso de controles nativos de cada plataforma. `ImageCell` difiere de `TextCell` que muestra una imagen a la izquierda del texto.

`ImageCell` es útil cuando se necesita mostrar una lista de los datos con un aspecto visual, como una lista de contactos o películas. ImageCells son personalizables, lo que permite establecer:

- `Text` &ndash; el texto que se muestra en la primera línea, en el tamaño de fuente
- `Detail` &ndash; el texto que se muestra debajo de la primera línea, en una fuente menor
- `TextColor` &ndash; el color del texto
- `DetailColor` &ndash; el color del texto de detalle
- `ImageSource` &ndash; la imagen que se muestra al lado del texto

![](customizing-cell-appearance-images/image-cell-default.png "Ejemplo de ImageCell predeterminado")

![](customizing-cell-appearance-images/image-cell-custom.png "Ejemplo de ImageCell personalizado")

<a name="customcells" />

## <a name="custom-cells"></a>Celdas personalizadas
Cuando las celdas integradas no proporcionan el diseño necesarias, celdas personalizadas implementan el diseño necesarias. Por ejemplo, desea presentar una celda con dos etiquetas que tengan el mismo peso. Un `TextCell` sería suficiente porque la `TextCell` tiene una etiqueta que sea más pequeña. La mayoría de las personalizaciones de celda agregan datos adicionales de solo lectura (por ejemplo, etiquetas adicionales, imágenes u otra información de presentación).

Todas las celdas personalizadas deben derivarse de [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), la misma clase base que todos los de la celda integrada tipos de uso.

Xamarin.Forms 2 presentó un nuevo [comportamiento de almacenamiento en caché](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy) en el `ListView` control que se puede establecer para mejorar el rendimiento de desplazamiento para algunos tipos de celdas personalizadas.

Este es un ejemplo de una celda personalizada:

![](customizing-cell-appearance-images/custom-cell.png "Ejemplo de la celda personalizada")

### <a name="xaml"></a>XAML
El XAML para crear el diseño anterior está por debajo:

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

El XAML anterior hace mucho. Permítame desglosarlo:

- La celda personalizada está anidada dentro de un `DataTemplate`, que está dentro de `ListView.ItemTemplate`. Este es el mismo proceso que con cualquier otra celda.
- `ViewCell` es el tipo de la celda personalizada. El elemento secundario de la `DataTemplate` debe ser de elemento o se derivan del tipo `ViewCell`.
- Tenga en cuenta que dentro del `ViewCell`, diseño está administrado por un `StackLayout`. Este diseño permite personalizar el color de fondo. Tenga en cuenta que cualquier propiedad de `StackLayout` es enlazable pueden ser enlazadas dentro de una celda personalizada, aunque no se muestra aquí.
- Dentro de `ViewCell`, el diseño se puede administrar con cualquier diseño de Xamarin. Forms. 

### <a name="cnum"></a>C&num;

Especificación de una celda personalizada en C# es un poco más detallado que el equivalente XAML. Observemos lo siguiente:

En primer lugar, defina una clase de celda personalizada, con `ViewCell` como clase base:

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

En el constructor de la página con el `ListView`, establecer el ListView `ItemTemplate` propiedad a un nuevo `DataTemplate`:

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

Tenga en cuenta que el constructor de `DataTemplate` toma un tipo. El operador typeof Obtiene el tipo CLR para `CustomCell`.

<a name="binding-context-changes" />

### <a name="binding-context-changes"></a>Cambios de contexto de enlace

Al enlazar a un tipo de celda personalizado [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instancias, los controles de interfaz de usuario mostrar el `BindableProperty` valores deben usar el [ `OnBindingContextChanged` ](xref:Xamarin.Forms.Cell.OnBindingContextChanged) invalide para establecer los datos que se mostrará en cada celda, en lugar de al constructor de la celda, como se muestra en el ejemplo de código siguiente:

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

    public string Name {
        get { return(string)GetValue (NameProperty); }
        set { SetValue (NameProperty, value); }
    }

    public int Age {
        get { return(int)GetValue (AgeProperty); }
        set { SetValue (AgeProperty, value); }
    }

    public string Location {
        get { return(string)GetValue (LocationProperty); }
        set { SetValue (LocationProperty, value); }
    }
    ...

    protected override void OnBindingContextChanged ()
    {
        base.OnBindingContextChanged ();

        if (BindingContext != null) {
            nameLabel.Text = Name;
            ageLabel.Text = Age.ToString ();
            locationLabel.Text = Location;
        }
    }
}
```

El [ `OnBindingContextChanged` ](xref:Xamarin.Forms.Cell.OnBindingContextChanged) invalidación llamará cuando el [ `BindingContextChanged` ](xref:Xamarin.Forms.BindableObject.BindingContextChanged) se activa el evento, en respuesta al valor de la [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) cambio de propiedad. Por lo tanto, cuando el `BindingContext` cambia, los controles de interfaz de usuario muestra el [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) valores deben establecer sus datos. Tenga en cuenta que el `BindingContext` debe comprobarse una `null` valor, tal como se puede establecer mediante Xamarin.Forms para la recolección, lo que resultará en la `OnBindingContextChanged` invalidación que se llama.

Como alternativa, pueden enlazar controles de interfaz de usuario a la [ `BindableProperty` ](xref:Xamarin.Forms.BindableProperty) instancias para mostrar sus valores, lo que elimina la necesidad de reemplazar el `OnBindingContextChanged` método.

> [!NOTE]
> Cuando se reemplaza `OnBindingContextChanged`, asegúrese de que la clase base `OnBindingContextChanged` se llama al método para que los delegados registrados reciban el `BindingContextChanged` eventos.

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

Esto enlaza el `Name`, `Age`, y `Location` propiedades enlazables en el `CustomCell` instancia, el `Name`, `Age`, y `Location` las propiedades de cada objeto de la colección subyacente.

El enlace equivalente en C# se muestra en el ejemplo de código siguiente:

```csharp
var customCell = new DataTemplate (typeof(CustomCell));
customCell.SetBinding (CustomCell.NameProperty, "Name");
customCell.SetBinding (CustomCell.AgeProperty, "Age");
customCell.SetBinding (CustomCell.LocationProperty, "Location");

var listView = new ListView {
    ItemsSource = people,
    ItemTemplate = customCell
};
```

En iOS y Android, si la [ `ListView` ](xref:Xamarin.Forms.ListView) se recicla de elementos y la celda personalizada usa un representador personalizado, el representador personalizado debe implementar correctamente la notificación de cambio de propiedad. Cuando se reutilizan las celdas sus valores de propiedad cambiará cuando se actualiza el contexto de enlace a la de una celda disponible, con `PropertyChanged` eventos provocados. Para obtener más información, consulte [personalizar ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md). Para obtener más información acerca de la celda de reciclaje, consulte [estrategia de almacenamiento en caché](~/xamarin-forms/user-interface/listview/performance.md#cachingstrategy).

## <a name="related-links"></a>Vínculos relacionados

- [Integrado en las celdas (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Celdas personalizadas (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Cambiar de contexto de enlace (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)
