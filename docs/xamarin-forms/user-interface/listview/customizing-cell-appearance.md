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
ms.sourcegitcommit: a5ef4497db04dfa016865bc7454b3de6ff088554
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998114"
---
# <a name="customizing-listview-cell-appearance"></a>Personalizar la apariencia de una celda de ListView

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)

La clase de Xamarin [`ListView`](xref:Xamarin.Forms.ListView) . Forms se usa para presentar listas desplazables, que se pueden personalizar mediante `ViewCell` el uso de elementos. Un `ViewCell` elemento puede mostrar texto e imágenes, indicar un estado verdadero/falso y recibir datos proporcionados por el usuario.

## <a name="built-in-cells"></a>Integrado en celdas
Xamarin. Forms incluye celdas integradas que funcionan para muchas aplicaciones:

- [`TextCell`](#textcell)los controles se usan para mostrar texto con una segunda línea opcional para el texto detallado.
- [`ImageCell`](#imagecell)los controles son similares `TextCell`a s, pero incluyen una imagen a la izquierda del texto.
- `SwitchCell`los controles se usan para presentar y capturar Estados de activado/desactivado o verdadero/falso.
- `EntryCell`los controles se usan para presentar datos de texto que el usuario puede editar.

Los [`SwitchCell`](~/xamarin-forms/user-interface/tableview.md#switchcell) controles [`EntryCell`](~/xamarin-forms/user-interface/tableview.md#entrycell) y se utilizan con más frecuencia en el contexto de [`TableView`](~/xamarin-forms/user-interface/tableview.md)un.

### <a name="textcell"></a>TextCell

[`TextCell`](xref:Xamarin.Forms.TextCell) es una celda para mostrar texto, opcionalmente con una segunda línea como texto de detalle. En la captura de `TextCell` pantalla siguiente se muestran los elementos de iOS y Android:

![](customizing-cell-appearance-images/text-cell-default.png "Ejemplo de TextCell predeterminado")

TextCells se representan como controles nativos en tiempo de ejecución, por lo que es muy buen rendimiento en comparación con una personalizada `ViewCell`. Los TextCells son personalizables, lo que le permite establecer las siguientes propiedades:

- `Text` &ndash; el texto que se muestra en la primera línea, en el tamaño de fuente.
- `Detail` &ndash; el texto que se muestra debajo de la primera línea, en una fuente menor.
- `TextColor` &ndash; el color del texto.
- `DetailColor` &ndash; el color del texto de detalle

La siguiente captura de `TextCell` pantalla muestra los elementos con propiedades de color personalizadas:

![](customizing-cell-appearance-images/text-cell-custom.png "Ejemplo de TextCell personalizado")

### <a name="imagecell"></a>ImageCell

[`ImageCell`](xref:Xamarin.Forms.ImageCell), como `TextCell`, se puede usar para mostrar texto y texto de detalles secundario y ofrece un excelente rendimiento mediante el uso de controles nativos de cada plataforma. `ImageCell` difiere de `TextCell` que muestra una imagen a la izquierda del texto.

En la captura de `ImageCell` pantalla siguiente se muestran los elementos de iOS y Android: !["Ejemplo predeterminado de ImageCell"](customizing-cell-appearance-images/image-cell-default.png "Ejemplo de ImageCell predeterminado")

`ImageCell` es útil cuando se necesita mostrar una lista de los datos con un aspecto visual, como una lista de contactos o películas. `ImageCell`los s son personalizables, lo que permite establecer:

- `Text` &ndash; el texto que se muestra en la primera línea, en el tamaño de fuente
- `Detail` &ndash; el texto que se muestra debajo de la primera línea, en una fuente menor
- `TextColor` &ndash; el color del texto
- `DetailColor` &ndash; el color del texto de detalle
- `ImageSource` &ndash; la imagen que se muestra al lado del texto

La siguiente captura de `ImageCell` pantalla muestra los elementos con propiedades de color personalizadas: !["Ejemplo de ImageCell personalizado"](customizing-cell-appearance-images/image-cell-custom.png "Ejemplo de ImageCell personalizado")

## <a name="custom-cells"></a>Celdas personalizadas
Las celdas personalizadas permiten crear diseños de celda que no son compatibles con las celdas integradas. Por ejemplo, desea presentar una celda con dos etiquetas que tengan el mismo peso. Un `TextCell` sería suficiente porque la `TextCell` tiene una etiqueta que sea más pequeña. La mayoría de las personalizaciones de celda agregan datos adicionales de solo lectura (por ejemplo, etiquetas adicionales, imágenes u otra información de presentación).

Todas las celdas personalizadas deben derivarse de [ `ViewCell` ](xref:Xamarin.Forms.ViewCell), la misma clase base que todos los de la celda integrada tipos de uso.

Xamarin. Forms ofrece un comportamiento de almacenamiento `ListView` en [caché](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy) en el control que puede mejorar el rendimiento del desplazamiento para algunos tipos de celdas personalizadas.

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

- La celda personalizada está anidada dentro de un `DataTemplate`, que está dentro de `ListView.ItemTemplate`. Este es el mismo proceso que el uso de cualquier celda integrada.
- `ViewCell` es el tipo de la celda personalizada. El `DataTemplate` elemento secundario del elemento debe ser de la clase o derivarse de `ViewCell` ella.
- Dentro de `ViewCell`, el diseño se puede administrar con cualquier diseño de Xamarin. Forms. En este ejemplo, el diseño se administra mediante `StackLayout`un, que permite personalizar el color de fondo.

> [!NOTE]
> Cualquier propiedad de `StackLayout` que se pueda enlazar se puede enlazar dentro de una celda personalizada. Sin embargo, esta funcionalidad no se muestra en el ejemplo de XAML.

### <a name="code"></a>Código

También se puede crear una celda personalizada en el código. En primer lugar, se debe crear una clase `ViewCell` personalizada derivada de:

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

En el constructor de la página, la `ItemTemplate` propiedad del objeto ListView está `DataTemplate` establecida en `CustomCell` con el tipo especificado:

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

var listView = new ListView
{
    ItemsSource = people,
    ItemTemplate = customCell
};
```

En iOS y Android, si la [ `ListView` ](xref:Xamarin.Forms.ListView) se recicla de elementos y la celda personalizada usa un representador personalizado, el representador personalizado debe implementar correctamente la notificación de cambio de propiedad. Cuando se reutilizan las celdas sus valores de propiedad cambiará cuando se actualiza el contexto de enlace a la de una celda disponible, con `PropertyChanged` eventos provocados. Para obtener más información, consulte [personalizar ViewCell](~/xamarin-forms/app-fundamentals/custom-renderer/viewcell.md). Para obtener más información acerca de la celda de reciclaje, consulte [estrategia de almacenamiento en caché](~/xamarin-forms/user-interface/listview/performance.md#caching-strategy).

## <a name="related-links"></a>Vínculos relacionados

- [Integrado en las celdas (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-builtincells)
- [Celdas personalizadas (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-customcells)
- [Cambiar de contexto de enlace (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-bindingcontextchanged)
