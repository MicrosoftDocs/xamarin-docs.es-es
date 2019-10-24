---
title: Agrupación de CollectionView de Xamarin. Forms
description: CollectionView puede mostrar los datos agrupados correctamente estableciendo su propiedad IsGrouped en true.
ms.prod: xamarin
ms.assetid: 7E494245-FDBD-49D6-B7FA-CEF976EB59BB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
ms.openlocfilehash: 3a0fe7159e6af24d58e49dea4166d012605c9985
ms.sourcegitcommit: e71474f91639bb43159b22f5d534325c3270ba93
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2019
ms.locfileid: "72749804"
---
# <a name="xamarinforms-collectionview-grouping"></a>Agrupación de CollectionView de Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

A menudo, los conjuntos de datos de gran tamaño pueden resultar difíciles de manejar cuando se presentan en una lista de desplazamiento continuo. En este escenario, la organización de los datos en grupos puede mejorar la experiencia del usuario al facilitar la navegación por los datos.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) admite la visualización de datos agrupados y define las siguientes propiedades que controlan cómo se presentarán:

- `IsGrouped`, de tipo `bool`, indica si los datos subyacentes se deben mostrar en grupos. El valor predeterminado de esta propiedad es `false`.
- `GroupHeaderTemplate`, de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), la plantilla que se va a usar para el encabezado de cada grupo.
- `GroupFooterTemplate`, de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), la plantilla que se va a utilizar para el pie de página de cada grupo.

Estas propiedades están respaldadas por objetos [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , lo que significa que las propiedades pueden ser destinos de enlaces de datos.

Las capturas de pantallas siguientes muestran un [`CollectionView`](xref:Xamarin.Forms.CollectionView) Mostrar datos agrupados:

[![Captura de pantalla de los datos agrupados en una CollectionView, en iOS y Android](grouping-images/grouped-data.png "CollectionView con datos agrupados")](grouping-images/grouped-data-large.png#lightbox "CollectionView con datos agrupados")

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="group-data"></a>Agrupar datos

Los datos se deben agrupar antes de que se puedan mostrar. Esto puede realizarse mediante la creación de una lista de grupos, donde cada grupo es una lista de elementos. La lista de grupos debe ser una colección de `IEnumerable<T>`, donde `T` define dos partes de datos:

- Un nombre de grupo.
- Colección de `IEnumerable` que define los elementos que pertenecen al grupo.

El proceso de agrupación de datos, por lo tanto, es:

- Cree un tipo que modela un solo elemento.
- Cree un tipo que modela un solo grupo de elementos.
- Cree una colección de `IEnumerable<T>`, donde `T` es el tipo que modela un solo grupo de elementos. Por lo tanto, esta colección es una colección de grupos, que almacena los datos agrupados.
- Agregue datos a la colección de `IEnumerable<T>`.

### <a name="example"></a>Ejemplo

Al agrupar los datos, el primer paso es crear un tipo que modela un solo elemento. En el ejemplo siguiente se muestra la clase `Animal` de la aplicación de ejemplo:

```csharp
public class Animal
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

La clase `Animal` modela un elemento único. Se puede crear un tipo que modela un grupo de elementos. En el ejemplo siguiente se muestra la clase `AnimalGroup` de la aplicación de ejemplo:

```csharp
public class AnimalGroup : List<Animal>
{
    public string Name { get; private set; }

    public AnimalGroup(string name, List<Animal> animals) : base(animals)
    {
        Name = name;
    }
}
```

La clase `AnimalGroup` hereda de la clase `List<T>` y agrega una propiedad `Name` que representa el nombre del grupo.

A continuación, se puede crear una colección `IEnumerable<T>` de grupos:

```csharp
public List<AnimalGroup> Animals { get; private set; } = new List<AnimalGroup>();
```

Este código define una colección denominada `Animals`, donde cada elemento de la colección es un objeto `AnimalGroup`. Cada objeto de `AnimalGroup` incluye un nombre y una colección de `List<Animal>` que define los objetos `Animal` del grupo.

Después, los datos agrupados se pueden agregar a la colección de `Animals`:

```csharp
Animals.Add(new AnimalGroup("Bears", new List<Animal>
{
    new Animal
    {
        Name = "American Black Bear",
        Location = "North America",
        Details = "Details about the bear go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/0/08/01_Schwarzbär.jpg"
    },
    new Animal
    {
        Name = "Asian Black Bear",
        Location = "Asia",
        Details = "Details about the bear go here.",
        ImageUrl = "https://upload.wikimedia.org/wikipedia/commons/thumb/b/b7/Ursus_thibetanus_3_%28Wroclaw_zoo%29.JPG/180px-Ursus_thibetanus_3_%28Wroclaw_zoo%29.JPG"
    },
    // ...
}));

Animals.Add(new AnimalGroup("Monkeys", new List<Animal>
{
    new Animal
    {
        Name = "Baboon",
        Location = "Africa & Asia",
        Details = "Details about the monkey go here.",
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/f/fc/Papio_anubis_%28Serengeti%2C_2009%29.jpg/200px-Papio_anubis_%28Serengeti%2C_2009%29.jpg"
    },
    new Animal
    {
        Name = "Capuchin Monkey",
        Location = "Central & South America",
        Details = "Details about the monkey go here.",
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/4/40/Capuchin_Costa_Rica.jpg/200px-Capuchin_Costa_Rica.jpg"
    },
    new Animal
    {
        Name = "Blue Monkey",
        Location = "Central and East Africa",
        Details = "Details about the monkey go here.",
        ImageUrl = "http://upload.wikimedia.org/wikipedia/commons/thumb/8/83/BlueMonkey.jpg/220px-BlueMonkey.jpg"
    },
    // ...
}));
```

Este código crea dos grupos en la colección de `Animals`. La primera `AnimalGroup` se denomina `Bears` y contiene una colección `List<Animal>` de detalles de los ositos. El segundo `AnimalGroup` se denomina `Monkeys` y contiene una colección `List<Animal>` de detalles de Monkey.

## <a name="display-grouped-data"></a>Mostrar datos agrupados

[`CollectionView`](xref:Xamarin.Forms.CollectionView) mostrará los datos agrupados, siempre que los datos se hayan agrupado correctamente, estableciendo la propiedad `IsGrouped` en `true`:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    <CollectionView.ItemTemplate>
        <DataTemplate>
            <Grid Padding="10">
                ...
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

El código de C# equivalente es el siguiente:

```csharp
CollectionView collectionView = new CollectionView
{
    IsGrouped = true
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
// ...
```

La apariencia de cada elemento del [`CollectionView`](xref:Xamarin.Forms.CollectionView) se define estableciendo la propiedad [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Para obtener más información, vea [definir la apariencia](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance)de los elementos.

> [!NOTE]
> De forma predeterminada, [`CollectionView`](xref:Xamarin.Forms.CollectionView) mostrará el nombre del grupo en el encabezado y pie de grupo. Este comportamiento se puede cambiar personalizando el encabezado de grupo y el pie de grupo.

## <a name="customize-the-group-header"></a>Personalizar el encabezado de grupo

La apariencia de cada encabezado de grupo se puede personalizar estableciendo la propiedad `CollectionView.GroupHeaderTemplate` en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    ...
    <CollectionView.GroupHeaderTemplate>
        <DataTemplate>
            <Label Text="{Binding Name}"
                   BackgroundColor="LightGray"
                   FontSize="Large"
                   FontAttributes="Bold" />
        </DataTemplate>
    </CollectionView.GroupHeaderTemplate>
</CollectionView>
```

En este ejemplo, cada encabezado de grupo se establece en un [`Label`](xref:Xamarin.Forms.Label) que muestra el nombre del grupo y que tiene establecidas otras propiedades de apariencia. Las capturas de pantallas siguientes muestran el encabezado de grupo personalizado:

[![Captura de pantalla de un encabezado de grupo personalizado en una CollectionView, en iOS y Android](grouping-images/customized-header.png "CollectionView con encabezado de grupo personalizado")](grouping-images/customized-header-large.png#lightbox "CollectionView con encabezado de grupo personalizado")

## <a name="customize-the-group-footer"></a>Personalizar el pie de grupo

La apariencia de cada pie de grupo se puede personalizar estableciendo la propiedad `CollectionView.GroupFooterTemplate` en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true">
    ...
    <CollectionView.GroupFooterTemplate>
        <DataTemplate>
            <Label Text="{Binding Count, StringFormat='Total animals: {0:D}'}"
                   Margin="0,0,0,10" />
        </DataTemplate>
    </CollectionView.GroupFooterTemplate>
</CollectionView>
```

En este ejemplo, cada pie de grupo se establece en un [`Label`](xref:Xamarin.Forms.Label) que muestra el número de elementos del grupo. Las capturas de pantallas siguientes muestran el pie de grupo personalizado:

[![Captura de pantalla de un pie de grupo personalizado en una CollectionView, en iOS y Android](grouping-images/customized-footer.png "CollectionView con pie de grupo personalizado")](grouping-images/customized-footer-large.png#lightbox "CollectionView con pie de grupo personalizado")

## <a name="empty-groups"></a>Grupos vacíos

Cuando un [`CollectionView`](xref:Xamarin.Forms.CollectionView) muestra los datos agrupados, mostrará los grupos que estén vacíos. Estos grupos se mostrarán con un encabezado y un pie de grupo, lo que indica que el grupo está vacío. Las capturas de pantallas siguientes muestran un grupo vacío:

[![Captura de pantalla de un grupo vacío en un CollectionView, en iOS y Android](grouping-images/empty-group.png "CollectionView con un grupo vacío")](grouping-images/empty-group-large.png#lightbox "CollectionView con un grupo vacío")

> [!NOTE]
> En iOS 10 y versiones anteriores, los encabezados y pies de grupo de los grupos vacíos se pueden mostrar en la parte superior de la `CollectionView`.

## <a name="group-without-templates"></a>Grupo sin plantillas

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pueden mostrar datos agrupados correctamente sin establecer la propiedad [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true" />
```

En este escenario, se pueden mostrar datos significativos invalidando el método `ToString` en el tipo que modela un elemento único y el tipo que modela un solo grupo de elementos.

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Plantillas de datos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
