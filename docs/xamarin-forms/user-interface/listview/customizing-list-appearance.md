---
title: Apariencia de ListView
description: En este artículo se explica cómo personalizar ListView en aplicaciones de Xamarin. Forms mediante encabezados, pies de página, grupos y celdas de alto variable.
ms.prod: xamarin
ms.assetid: DC8009B0-4371-4D60-885A-5362FC7EE3E5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/13/2018
ms.openlocfilehash: 90b0e0f3802ce766decb802c9406d72b5966360e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032815"
---
# <a name="listview-appearance"></a>Apariencia de ListView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)

El [`ListView`](xref:Xamarin.Forms.ListView) de Xamarin. Forms permite personalizar la presentación de la lista, además de las instancias de [`ViewCell`](xref:Xamarin.Forms.ViewCell) para cada fila de la lista.

## <a name="grouping"></a>Agrupar

Los grandes conjuntos de datos pueden resultar difíciles de manejar cuando se presentan en una lista de desplazamiento continuo. La habilitación de la agrupación puede mejorar la experiencia del usuario en estos casos al organizar mejor el contenido y activar controles específicos de la plataforma que facilitan la navegación por los datos.

Cuando se activa la agrupación para un `ListView`, se agrega una fila de encabezado para cada grupo.

Para habilitar la agrupación:

- Cree una lista de listas (una lista de grupos, cada grupo es una lista de elementos).
- Establezca la `ItemsSource` del `ListView`en esa lista.
- Establezca `IsGroupingEnabled` en true.
- Establezca [`GroupDisplayBinding`](xref:Xamarin.Forms.ListView.GroupDisplayBinding) para enlazar a la propiedad de los grupos que se utiliza como título del grupo.
- Opta Establezca [`GroupShortNameBinding`](xref:Xamarin.Forms.ListView.GroupShortNameBinding) para enlazar a la propiedad de los grupos que se usa como nombre corto para el grupo. El nombre corto se usa para las Jump Lists (columna derecha en iOS).

Empiece por crear una clase para los grupos:

```csharp
public class PageTypeGroup : List<PageModel>
    {
        public string Title { get; set; }
        public string ShortName { get; set; } //will be used for jump lists
        public string Subtitle { get; set; }
        private PageTypeGroup(string title, string shortName)
        {
            Title = title;
            ShortName = shortName;
        }

        public static IList<PageTypeGroup> All { private set; get; }
    }
```

En el código anterior, `All` es la lista que se proporcionará a nuestro ListView como el origen de enlace. `Title` y `ShortName` son las propiedades que se utilizarán para los encabezados de grupo.

En esta fase, `All` es una lista vacía. Agregue un constructor estático para que la lista se rellene al iniciar el programa:

```csharp
static PageTypeGroup()
{
    List<PageTypeGroup> Groups = new List<PageTypeGroup> {
            new PageTypeGroup ("Alpha", "A"){
                new PageModel("Amelia", "Cedar", new switchCellPage(),""),
                new PageModel("Alfie", "Spruce", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Ava", "Pine", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Archie", "Maple", new switchCellPage(), "grapefruit.jpg")
            },
            new PageTypeGroup ("Bravo", "B"){
                new PageModel("Brooke", "Lumia", new switchCellPage(),""),
                new PageModel("Bobby", "Xperia", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Bella", "Desire", new switchCellPage(), "grapefruit.jpg"),
                new PageModel("Ben", "Chocolate", new switchCellPage(), "grapefruit.jpg")
            }
        };
        All = Groups; //set the publicly accessible list
}
```

En el código anterior, también se puede llamar a `Add` en elementos de `Groups`, que son instancias de tipo `PageTypeGroup`. Este método es posible porque `PageTypeGroup` hereda de `List<PageModel>`.

Este es el código XAML para mostrar la lista agrupada:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DemoListView.GroupingViewPage"
    <ContentPage.Content>
        <ListView  x:Name="GroupedView"
                   GroupDisplayBinding="{Binding Title}"
                   GroupShortNameBinding="{Binding ShortName}"
                   IsGroupingEnabled="true">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                              Detail="{Binding Subtitle}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

Este código XAML realiza las siguientes acciones:

- Establezca `GroupShortNameBinding` en la propiedad `ShortName` definida en la clase Group.
- Establezca `GroupDisplayBinding` en la propiedad `Title` definida en la clase Group.
- Establezca `IsGroupingEnabled` en true
- Se ha cambiado el `ItemsSource` del `ListView`a la lista agrupada

En la siguiente captura de pantalla se muestra la interfaz de usuario resultante:

![](customizing-list-appearance-images/grouping-depth.png "ListView Grouping Example")

### <a name="customizing-grouping"></a>Personalización de la agrupación

Si se ha habilitado la agrupación en la lista, el encabezado de grupo también se puede personalizar.

De forma similar al modo en que el `ListView` tiene un `ItemTemplate` para definir cómo se muestran las filas, `ListView` tiene un `GroupHeaderTemplate`.

A continuación se muestra un ejemplo de personalización del encabezado de grupo en XAML:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="DemoListView.GroupingViewPage">
    <ContentPage.Content>
        <ListView x:Name="GroupedView"
                  GroupDisplayBinding="{Binding Title}"
                  GroupShortNameBinding="{Binding ShortName}"
                  IsGroupingEnabled="true">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                              Detail="{Binding Subtitle}"
                              TextColor="#f35e20"
                              DetailColor="#503026" />
                </DataTemplate>
            </ListView.ItemTemplate>
            <!-- Group Header Customization-->
            <ListView.GroupHeaderTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Title}"
                              Detail="{Binding ShortName}"
                              TextColor="#f35e20"
                              DetailColor="#503026" />
                </DataTemplate>
            </ListView.GroupHeaderTemplate>
            <!-- End Group Header Customization -->
        </ListView>
    </ContentPage.Content>
</ContentPage>
```

## <a name="headers-and-footers"></a>Encabezados y pies de página

Es posible que un control ListView presente un encabezado y un pie de página que se desplacen con los elementos de la lista. El encabezado y el pie de página pueden ser cadenas de texto o un diseño más complicado. Este comportamiento es independiente de los [grupos de sección](#grouping).

Puede establecer el `Header` o `Footer` en un valor de `string`, o bien puede establecerlos en un diseño más complejo. También hay `HeaderTemplate` y `FooterTemplate` propiedades que permiten crear diseños más complejos para el encabezado y el pie de página que admiten el enlace de datos.

Para crear un encabezado/pie de página básico, solo tiene que establecer las propiedades de encabezado o de pie de página en el texto que desea mostrar. Mediante código:

```csharp
ListView HeaderList = new ListView()
{
    Header = "Header",
    Footer = "Footer"
};
```

En XAML:

```xaml
<ListView x:Name="HeaderList" 
          Header="Header"
          Footer="Footer">
    ...
</ListView>
```

![](customizing-list-appearance-images/header-default.png "ListView with Header and Footer")

Para crear un encabezado y un pie de página personalizados, defina las vistas de encabezado y pie de página:

```xaml
<ListView.Header>
    <StackLayout Orientation="Horizontal">
        <Label Text="Header"
               TextColor="Olive"
               BackgroundColor="Red" />
    </StackLayout>
</ListView.Header>
<ListView.Footer>
    <StackLayout Orientation="Horizontal">
        <Label Text="Footer"
               TextColor="Gray"
               BackgroundColor="Blue" />
    </StackLayout>
</ListView.Footer>
```

![](customizing-list-appearance-images/header-custom.png "ListView with Customized Header and Footer")

## <a name="scrollbar-visibility"></a>Visibilidad de ScrollBar

La clase [`ListView`](xref:Xamarin.Forms.ListView) tiene `HorizontalScrollBarVisibility` y `VerticalScrollBarVisibility` propiedades, que obtienen o establecen un valor [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) que representa Cuándo está visible la barra de desplazamiento horizontal o vertical. Ambas propiedades se pueden establecer en los siguientes valores:

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility) indica el comportamiento predeterminado de la barra de desplazamiento para la plataforma y es el valor predeterminado para las propiedades `HorizontalScrollBarVisibility` y `VerticalScrollBarVisibility`.
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility) indica que las barras de desplazamiento serán visibles, incluso cuando el contenido quepa en la vista.
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility) indica que las barras de desplazamiento no estarán visibles, incluso si el contenido no cabe en la vista.

## <a name="row-separators"></a>Separadores de filas

Las líneas separadoras se muestran entre los elementos `ListView` de forma predeterminada en iOS y Android. Si prefiere ocultar las líneas de separación en iOS y Android, establezca la propiedad `SeparatorVisibility` en el control ListView. Las opciones para `SeparatorVisibility` son:

- **Predeterminada** : muestra una línea de separación en iOS y Android.
- **Ninguno** : oculta el separador en todas las plataformas.

Visibilidad predeterminada:

C#:

```csharp
SeparatorDemoListView.SeparatorVisibility = SeparatorVisibility.Default;
```

LENGUAJE

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="Default" />
```

![](customizing-list-appearance-images/separator-default.png "ListView with Default Row Separators")

Ninguno:

C#:

```csharp
SeparatorDemoListView.SeparatorVisibility = SeparatorVisibility.None;
```

LENGUAJE

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorVisibility="None" />
```

![](customizing-list-appearance-images/separator-none.png "ListView without Row Separators")

También puede establecer el color de la línea de separación a través de la propiedad `SeparatorColor`:

C#:

```csharp
SeparatorDemoListView.SeparatorColor = Color.Green;
```

LENGUAJE

```xaml
<ListView x:Name="SeparatorDemoListView" SeparatorColor="Green" />
```

![](customizing-list-appearance-images/separator-custom.png "ListView with Green Row Separators")

> [!NOTE]
> Establecer cualquiera de estas propiedades en Android después de cargar el `ListView` incurre en una gran penalización del rendimiento.

## <a name="row-height"></a>Alto de fila

Todas las filas de un control ListView tienen el mismo alto de forma predeterminada. ListView tiene dos propiedades que se pueden usar para cambiar este comportamiento:

- `HasUnevenRows` &ndash; `true`/`false` valor, las filas tienen alturas variables si están establecidas en `true`. Tiene como valor predeterminado `false`.
- `RowHeight` &ndash; establece el alto de cada fila cuando se `false``HasUnevenRows`.

Puede establecer el alto de todas las filas estableciendo la propiedad `RowHeight` en el `ListView`.

### <a name="custom-fixed-row-height"></a>Alto de fila fijo personalizado

C#:

```csharp
RowHeightDemoListView.RowHeight = 100;
```

LENGUAJE

```xaml
<ListView x:Name="RowHeightDemoListView" RowHeight="100" />
```

![](customizing-list-appearance-images/height-custom.png "ListView with Fixed Row Height")

### <a name="uneven-rows"></a>Filas desiguales

Si desea que las filas individuales tengan distintos altos, puede establecer la propiedad `HasUnevenRows` en `true`. No es necesario establecer manualmente el alto de las filas una vez `HasUnevenRows` se ha establecido en `true`, porque Xamarin. Forms calculará automáticamente el alto.

C#:

```csharp
RowHeightDemoListView.HasUnevenRows = true;
```

LENGUAJE

```xaml
<ListView x:Name="RowHeightDemoListView" HasUnevenRows="true" />
```

![](customizing-list-appearance-images/height-uneven.png "ListView with Uneven Rows")

### <a name="resize-rows-at-runtime"></a>Cambiar el tamaño de las filas en tiempo de ejecución

Se puede cambiar el tamaño de las filas `ListView` individuales mediante programación en tiempo de ejecución, siempre que la propiedad `HasUnevenRows` esté establecida en `true`. El método [`Cell.ForceUpdateSize`](xref:Xamarin.Forms.Cell.ForceUpdateSize) actualiza el tamaño de una celda, incluso cuando no está visible actualmente, como se muestra en el ejemplo de código siguiente:

```csharp
void OnImageTapped (object sender, EventArgs args)
{
    var image = sender as Image;
    var viewCell = image.Parent.Parent as ViewCell;

    if (image.HeightRequest < 250) {
        image.HeightRequest = image.Height + 100;
        viewCell.ForceUpdateSize ();
    }
}
```

El controlador de eventos `OnImageTapped` se ejecuta como respuesta a una [`Image`](xref:Xamarin.Forms.Image) en una celda que se está punteando y aumenta el tamaño de la `Image` mostrada en la celda para que se pueda ver fácilmente.

![](customizing-list-appearance-images/dynamic-row-resizing.png "ListView with Runtime Row Resizing")

> [!WARNING]
> El uso excesivo del cambio de tamaño de las filas en tiempo de ejecución puede degradar el rendimiento.

## <a name="related-links"></a>Vínculos relacionados

- [Agrupación (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-grouping)
- [Vista de representador personalizado (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithlistviewnative)
- [Cambio de tamaño dinámico de filas (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-dynamicunevenlistcells)
- [Notas de la versión de 1,4](https://forums.xamarin.com/discussion/35451/xamarin-forms-1-4-0-released/)
- [Notas de la versión de 1,3](https://forums.xamarin.com/discussion/29934/xamarin-forms-1-3-0-released/)
