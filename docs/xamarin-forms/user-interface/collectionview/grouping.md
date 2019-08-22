---
title: Agrupación de CollectionView de Xamarin. Forms
description: CollectionView puede mostrar los datos agrupados correctamente estableciendo su propiedad IsGrouped en true.
ms.prod: xamarin
ms.assetid: 7E494245-FDBD-49D6-B7FA-CEF976EB59BB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 8fd37999428c2813bbf96de3bcbd6ebd1fe0879d
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69894032"
---
# <a name="xamarinforms-collectionview-grouping"></a>Agrupación de CollectionView de Xamarin. Forms

![](~/media/shared/preview.png "Esta API se encuentra actualmente en versión preliminar")

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos)

A menudo, los conjuntos de datos de gran tamaño pueden resultar difíciles de manejar cuando se presentan en una lista de desplazamiento continuo. En este escenario, la organización de los datos en grupos puede mejorar la experiencia del usuario al facilitar la navegación por los datos.

[`CollectionView`](xref:Xamarin.Forms.CollectionView)admite la visualización de datos agrupados y define las siguientes propiedades que controlan cómo se presentará:

- `IsGrouped`, de tipo `bool`, indica si los datos subyacentes se deben mostrar en grupos. El valor predeterminado de esta propiedad es `false`.
- `GroupHeaderTemplate`, de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), la plantilla que se va a usar para el encabezado de cada grupo.
- `GroupFooterTemplate`, de tipo [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), la plantilla que se va a usar para el pie de página de cada grupo.

Estas propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que las propiedades pueden ser destinos de enlaces de datos.

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

> [!IMPORTANT]
> La agrupación de [`CollectionView`](xref:Xamarin.Forms.CollectionView) datos con solo se admite actualmente en iOS.

## <a name="group-data"></a>Agrupar datos

Los datos se deben agrupar antes de que se puedan mostrar. Esto puede realizarse mediante la creación de una lista de grupos, donde cada grupo es una lista de elementos. La lista de grupos debe ser una `IEnumerable<T>` colección, donde `T` define dos fragmentos de datos:

- Un nombre de grupo.
- `IEnumerable` Colección que define los elementos que pertenecen al grupo.

El proceso de agrupación de datos, por lo tanto, es:

- Cree un tipo que modela un solo elemento.
- Cree un tipo que modela un solo grupo de elementos.
- Cree una `IEnumerable<T>` colección, donde `T` es el tipo que modela un solo grupo de elementos. Por lo tanto, esta colección es una colección de grupos, que almacena los datos agrupados.
- Agregar datos a la `IEnumerable<T>` colección.

### <a name="example"></a>Ejemplo

Al agrupar los datos, el primer paso es crear un tipo que modela un solo elemento. En el ejemplo siguiente se `Animal` muestra la clase de la aplicación de ejemplo:

```csharp
public class Animal
{
    public string Name { get; set; }
    public string Location { get; set; }
    public string Details { get; set; }
    public string ImageUrl { get; set; }
}
```

La `Animal` clase modela un elemento único. Se puede crear un tipo que modela un grupo de elementos. En el ejemplo siguiente se `AnimalGroup` muestra la clase de la aplicación de ejemplo:

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

La `AnimalGroup` clase hereda de la `List<T>` clase y agrega una `Name` propiedad que representa el nombre del grupo.

Después `IEnumerable<T>` , se puede crear una colección de grupos:

```csharp
public List<AnimalGroup> Animals { get; private set; } = new List<AnimalGroup>();
```

Este código define una colección denominada `Animals`, donde cada elemento de la colección es un `AnimalGroup` objeto. Cada `AnimalGroup` objeto incluye un nombre y una `List<Animal>` colección que define los `Animal` objetos del grupo.

Después, los datos agrupados se pueden agregar `Animals` a la colección:

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

Este código crea dos grupos en la `Animals` colección. El primero `AnimalGroup` se denomina `Bears`y contiene una `List<Animal>` colección de detalles del osito. La segunda `AnimalGroup` se denomina `Monkeys`y contiene una `List<Animal>` colección de detalles de Monkey.

## <a name="display-grouped-data"></a>Mostrar datos agrupados

[`CollectionView`](xref:Xamarin.Forms.CollectionView)mostrará los datos agrupados, siempre que los datos se hayan agrupado correctamente, estableciendo la `IsGrouped` propiedad en: `true`

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

El código de C# equivalente es:

```csharp
CollectionView collectionView = new CollectionView
{
    IsGrouped = true
};
collectionView.SetBinding(ItemsView.ItemsSourceProperty, "Animals");
// ...
```

La apariencia de cada elemento de [`CollectionView`](xref:Xamarin.Forms.CollectionView) se define estableciendo la [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propiedad en [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Para obtener más información, vea [definir la apariencia](~/xamarin-forms/user-interface/collectionview/populate-data.md#define-item-appearance)de los elementos.

> [!NOTE]
> De forma predeterminada [`CollectionView`](xref:Xamarin.Forms.CollectionView) , mostrará el nombre del grupo en el encabezado y pie de grupo. Este comportamiento se puede cambiar personalizando el encabezado de grupo y el pie de grupo.

## <a name="customize-the-group-header"></a>Personalizar el encabezado de grupo

La apariencia de cada encabezado de grupo se puede personalizar estableciendo la `CollectionView.GroupHeaderTemplate` propiedad en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

En este ejemplo, cada encabezado de grupo se establece en [`Label`](xref:Xamarin.Forms.Label) un que muestra el nombre del grupo y que tiene establecidas otras propiedades de apariencia.

## <a name="customize-the-group-footer"></a>Personalizar el pie de grupo

La apariencia de cada pie de grupo se puede personalizar estableciendo la `CollectionView.GroupFooterTemplate` propiedad [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)en:

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

En este ejemplo, cada pie de grupo se establece en un [`Label`](xref:Xamarin.Forms.Label) valor de que muestra el número de elementos del grupo.

## <a name="empty-groups"></a>Grupos vacíos

Cuando un [`CollectionView`](xref:Xamarin.Forms.CollectionView) muestra los datos agrupados, mostrará los grupos que estén vacíos. Estos grupos se mostrarán con un encabezado y un pie de grupo, lo que indica que el grupo está vacío.

> [!NOTE]
> En iOS 10 y versiones anteriores, los encabezados y pies de grupo de los grupos vacíos se pueden mostrar en la parte `CollectionView`superior de.

## <a name="group-without-templates"></a>Grupo sin plantillas

[`CollectionView`](xref:Xamarin.Forms.CollectionView)puede mostrar datos agrupados correctamente sin establecer la [`CollectionView.ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) propiedad [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)en:

```xaml
<CollectionView ItemsSource="{Binding Animals}"
                IsGrouped="true" />
```

En este escenario, se pueden mostrar datos significativos invalidando el `ToString` método en el tipo que modela un elemento único y el tipo que modela un solo grupo de elementos.

## <a name="related-links"></a>Vínculos relacionados

- [CollectionView (ejemplo)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)
- [Plantillas de datos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
