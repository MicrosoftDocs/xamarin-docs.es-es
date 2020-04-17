---
title: Creación de una plantilla de datos de Xamarin.Forms
description: Las plantillas de datos se pueden crear insertadas, en un objeto ResourceDictionary, o bien a partir de un tipo personalizado o un tipo de celda de Xamarin.Forms adecuado. En este artículo se explora cada una de las técnicas.
ms.prod: xamarin
ms.assetid: CFF4AB5E-9069-461C-84D8-F9F6C38510AB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/11/2017
ms.openlocfilehash: 399f411acd497b9d55ca81f670556430fe5f5503
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771288"
---
# <a name="creating-a-xamarinforms-datatemplate"></a>Creación de una plantilla de datos de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)

_Las plantillas de datos se pueden crear insertadas, en un objeto ResourceDictionary, o bien a partir de un tipo personalizado o un tipo de celda de Xamarin.Forms adecuado. En este artículo se explora cada una de las técnicas._

Un escenario de uso común para un elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) es mostrar datos de una colección de objetos en un control [`ListView`](xref:Xamarin.Forms.ListView). La apariencia de los datos de cada celda del control [`ListView`](xref:Xamarin.Forms.ListView) se puede administrar mediante el establecimiento de la propiedad [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) en un elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Se pueden usar varias técnicas para realizar esta acción:

- [Creación de una plantilla de datos insertada](#inline).
- [Creación de una plantilla de datos con un tipo](#type).
- [Creación de una plantilla de datos como un recurso](#resource).

Independientemente de la técnica que se use, el resultado es que la apariencia de cada celda del control [`ListView`](xref:Xamarin.Forms.ListView) se define mediante un elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), como se muestra en las capturas de pantalla siguientes:

![](creating-images/data-template-appearance.png "ListView with a DataTemplate")

<a name="inline" />

## <a name="creating-an-inline-datatemplate"></a>Creación de una plantilla de datos insertada

La propiedad [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) se puede establecer en un elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) insertado. Una plantilla insertada, que es la que se coloca como elemento secundario directo de una propiedad de control adecuada, se debe usar si no hay ninguna necesidad de reutilizar la plantilla de datos en otros puntos. Los elementos especificados en el elemento `DataTemplate` definen la apariencia de cada celda, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<ListView Margin="0,20,0,0">
    <ListView.ItemsSource>
        <x:Array Type="{x:Type local:Person}">
            <local:Person Name="Steve" Age="21" Location="USA" />
            <local:Person Name="John" Age="37" Location="USA" />
            <local:Person Name="Tom" Age="42" Location="UK" />
            <local:Person Name="Lucas" Age="29" Location="Germany" />
            <local:Person Name="Tariq" Age="39" Location="UK" />
            <local:Person Name="Jane" Age="30" Location="USA" />
        </x:Array>
    </ListView.ItemsSource>
    <ListView.ItemTemplate>
        <DataTemplate>
            <ViewCell>
                <Grid>
                    ...
                    <Label Text="{Binding Name}" FontAttributes="Bold" />
                    <Label Grid.Column="1" Text="{Binding Age}" />
                    <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
                </Grid>
            </ViewCell>
        </DataTemplate>
    </ListView.ItemTemplate>
</ListView>
```

El elemento secundario de un elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) insertado debe ser de tipo [`Cell`](xref:Xamarin.Forms.Cell), o bien derivarse de él. En este ejemplo se usa un [`ViewCell`](xref:Xamarin.Forms.ViewCell), que procede de `Cell`. Aquí, el diseño dentro de `ViewCell` se administra mediante un control [`Grid`](xref:Xamarin.Forms.Grid). `Grid` contiene tres instancias de [`Label`](xref:Xamarin.Forms.Label) que enlazan sus propiedades [`Text`](xref:Xamarin.Forms.Label.Text) a las propiedades adecuadas de cada objeto `Person` de la colección.

El código de C# equivalente se muestra en el ejemplo de código siguiente:

```csharp
public class WithDataTemplatePageCS : ContentPage
{
    public WithDataTemplatePageCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        var personDataTemplate = new DataTemplate(() =>
        {
            var grid = new Grid();
            ...
            var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
            var ageLabel = new Label();
            var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

            nameLabel.SetBinding(Label.TextProperty, "Name");
            ageLabel.SetBinding(Label.TextProperty, "Age");
            locationLabel.SetBinding(Label.TextProperty, "Location");

            grid.Children.Add(nameLabel);
            grid.Children.Add(ageLabel, 1, 0);
            grid.Children.Add(locationLabel, 2, 0);

            return new ViewCell { View = grid };
        });

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemsSource = people, ItemTemplate = personDataTemplate, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

En C#, el elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) insertado se crea mediante una sobrecarga del constructor que especifica un argumento `Func`.

<a name="type" />

## <a name="creating-a-datatemplate-with-a-type"></a>Creación de una plantilla de datos con un tipo

La propiedad [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) también se puede establecer en un elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) creado a partir de un tipo de celda. La ventaja de este enfoque es que la apariencia definida por el tipo de celda se puede reutilizar en varias plantillas de datos en toda la aplicación. En el código XAML siguiente se muestra un ejemplo de este enfoque:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataTemplates"
             ...>
    <StackLayout Margin="20">
        ...
        <ListView Margin="0,20,0,0">
           <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    ...
                </x:Array>
            </ListView.ItemsSource>
            <ListView.ItemTemplate>
                <DataTemplate>
                    <local:PersonCell />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

En este caso, la propiedad [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) se establece en un elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) creado a partir de un tipo personalizado que define el aspecto de la celda. El tipo personalizado se debe derivar del tipo [`ViewCell`](xref:Xamarin.Forms.ViewCell), como se muestra en el ejemplo de código siguiente:

```xaml
<ViewCell xmlns="http://xamarin.com/schemas/2014/forms"
          xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
          x:Class="DataTemplates.PersonCell">
     <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="0.5*" />
            <ColumnDefinition Width="0.2*" />
            <ColumnDefinition Width="0.3*" />
        </Grid.ColumnDefinitions>
        <Label Text="{Binding Name}" FontAttributes="Bold" />
        <Label Grid.Column="1" Text="{Binding Age}" />
        <Label Grid.Column="2" Text="{Binding Location}" HorizontalTextAlignment="End" />
    </Grid>
</ViewCell>
```

Dentro de [`ViewCell`](xref:Xamarin.Forms.ViewCell), el diseño se administra mediante un control [`Grid`](xref:Xamarin.Forms.Grid). `Grid` contiene tres instancias de [`Label`](xref:Xamarin.Forms.Label) que enlazan sus propiedades [`Text`](xref:Xamarin.Forms.Label.Text) a las propiedades adecuadas de cada objeto `Person` de la colección.

El código de C# equivalente se muestra en el ejemplo siguiente:

```csharp
public class WithDataTemplatePageFromTypeCS : ContentPage
{
    public WithDataTemplatePageFromTypeCS()
    {
        ...
        var people = new List<Person>
        {
            new Person { Name = "Steve", Age = 21, Location = "USA" },
            ...
        };

        Content = new StackLayout
        {
            Margin = new Thickness(20),
            Children = {
                ...
                new ListView { ItemTemplate = new DataTemplate(typeof(PersonCellCS)), ItemsSource = people, Margin = new Thickness(0, 20, 0, 0) }
            }
        };
    }
}
```

En C#, [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) se crea mediante una sobrecarga del constructor que especifica el tipo de celda como argumento. El tipo de celda se debe derivar del tipo [`ViewCell`](xref:Xamarin.Forms.ViewCell), como se muestra en el ejemplo de código siguiente:

```csharp
public class PersonCellCS : ViewCell
{
    public PersonCellCS()
    {
        var grid = new Grid();
        ...
        var nameLabel = new Label { FontAttributes = FontAttributes.Bold };
        var ageLabel = new Label();
        var locationLabel = new Label { HorizontalTextAlignment = TextAlignment.End };

        nameLabel.SetBinding(Label.TextProperty, "Name");
        ageLabel.SetBinding(Label.TextProperty, "Age");
        locationLabel.SetBinding(Label.TextProperty, "Location");

        grid.Children.Add(nameLabel);
        grid.Children.Add(ageLabel, 1, 0);
        grid.Children.Add(locationLabel, 2, 0);

        View = grid;
    }
}
```

> [!NOTE]
> Tenga en cuenta que Xamarin.Forms también incluye tipos de celda que se pueden usar para mostrar datos simples en celdas [`ListView`](xref:Xamarin.Forms.ListView). Para obtener más información, vea [Apariencia de una celda](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

<a name="resource" />

## <a name="creating-a-datatemplate-as-a-resource"></a>Creación de una plantilla de datos con un recurso

Las plantillas de datos también se pueden crear como objetos reutilizables en un objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Esto se consigue mediante la asignación de un atributo `x:Key` único a cada declaración, para proporcionarle una clave descriptiva en el objeto `ResourceDictionary`, como se muestra en el ejemplo de código XAML siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="personTemplate">
                 <ViewCell>
                    <Grid>
                        ...
                    </Grid>
                </ViewCell>
            </DataTemplate>
        </ResourceDictionary>
    </ContentPage.Resources>
    <StackLayout Margin="20">
        ...
        <ListView ItemTemplate="{StaticResource personTemplate}" Margin="0,20,0,0">
            <ListView.ItemsSource>
                <x:Array Type="{x:Type local:Person}">
                    <local:Person Name="Steve" Age="21" Location="USA" />
                    ...
                </x:Array>
            </ListView.ItemsSource>
        </ListView>
    </StackLayout>
</ContentPage>
```

El elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) se asigna a la propiedad [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1) mediante la extensión de marcado `StaticResource`. Tenga en cuenta que aunque el elemento `DataTemplate` se defina en el objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de la página, también se puede definir en el nivel de control o aplicación.

En el ejemplo de código siguiente se muestra la página equivalente en C#:

```csharp
public class WithDataTemplatePageCS : ContentPage
{
  public WithDataTemplatePageCS ()
  {
    ...
    var personDataTemplate = new DataTemplate (() => {
      var grid = new Grid ();
      ...
      return new ViewCell { View = grid };
    });

    Resources = new ResourceDictionary ();
    Resources.Add ("personTemplate", personDataTemplate);

    Content = new StackLayout {
      Margin = new Thickness(20),
      Children = {
        ...
        new ListView { ItemTemplate = (DataTemplate)Resources ["personTemplate"], ItemsSource = people };
      }
    };
  }
}
```

El elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) se agrega a [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) con el método [`Add`](xref:Xamarin.Forms.ResourceDictionary.Add(System.String,System.Object)), que especifica una cadena `Key` que se usa para hacer referencia al elemento `DataTemplate` al recuperarlo.

## <a name="summary"></a>Resumen

En este artículo se ha explicado cómo crear plantillas de datos, insertadas, a partir de un tipo personalizado, o bien en un objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary). Una plantilla insertada se debe usar si no es necesario volver a utilizarla en otro lugar. Como alternativa, se puede reutilizar una plantilla de datos si se define como un tipo personalizado, o bien como un recurso de nivel de página, nivel de aplicación o de nivel de control.

## <a name="related-links"></a>Vínculos relacionados

- [Apariencia de etiqueta](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md)
- [Plantillas de datos (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-datatemplates)
- [DataTemplate](xref:Xamarin.Forms.DataTemplate)
