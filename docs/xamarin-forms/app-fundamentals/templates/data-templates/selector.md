---
title: Creación de DataTemplateSelector de Xamarin.Forms
description: En este artículo se muestra cómo crear y consumir un elemento DataTemplateSelector, que se puede usar para elegir un plantilla de datos en tiempo de ejecución según el valor de una propiedad enlazada a datos.
ms.prod: xamarin
ms.assetid: A4629E8F-2BAF-45CE-A76E-DF225FE8D26C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b072c04aa3d782b3bbbac0952102d0b1531e87dc
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373021"
---
# <a name="creating-a-xamarinforms-datatemplateselector"></a>Creación de DataTemplateSelector de Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector)

_Un elemento DataTemplateSelector se puede usar para elegir una plantilla de datos en tiempo de ejecución según el valor de una propiedad enlazada a datos. Esto permite aplicar varias instancias de DataTemplate al mismo tipo de objeto, para personalizar la apariencia de objetos concretos. En este artículo se explica cómo crear y consumir una instancia de DataTemplateSelector._

Un selector de plantillas de datos habilita escenarios como el enlace de [`ListView`](xref:Xamarin.Forms.ListView) a una colección de objetos, donde la apariencia de cada objeto de `ListView` se puede elegir en tiempo de ejecución mediante el selector de plantillas de datos devolviendo un elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) determinado.

## <a name="creating-a-datatemplateselector"></a>Creación de DataTemplateSelector

Un selector de plantillas de datos se implementa mediante la creación de una clase que hereda de [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector). Después, se reemplaza el método `OnSelectTemplate` para devolver un elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate), como se muestra en el ejemplo de código siguiente:

```csharp
public class PersonDataTemplateSelector : DataTemplateSelector
{
  public DataTemplate ValidTemplate { get; set; }
  public DataTemplate InvalidTemplate { get; set; }

  protected override DataTemplate OnSelectTemplate (object item, BindableObject container)
  {
    return ((Person)item).DateOfBirth.Year >= 1980 ? ValidTemplate : InvalidTemplate;
  }
}
```

El método `OnSelectTemplate` devuelve la plantilla adecuada en función del valor de la propiedad `DateOfBirth`. La plantilla que se devuelve es el valor de las propiedades `ValidTemplate` o `InvalidTemplate`, que se establecen cuando se consume `PersonDataTemplateSelector`.

Después, se puede asignar una instancia de la clase de selector de plantilla de datos a propiedades de control de Xamarin.Forms, como [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1). Para obtener una lista de las propiedades válidas, vea [Creación de una plantilla de datos](~/xamarin-forms/app-fundamentals/templates/data-templates/creating.md).

### <a name="limitations"></a>Limitaciones

Las instancias de [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) tienen las limitaciones siguientes:

- La subclase `DataTemplateSelector` siempre debe devolver la misma plantilla para los mismos datos si se consultan varias veces.
- La subclase `DataTemplateSelector` no debe devolver otra subclase `DataTemplateSelector`.
- La subclase `DataTemplateSelector` no debe devolver nuevas instancias de `DataTemplate` en cada llamada. En su lugar, se debe devolver la misma instancia. De lo contrario, se creará una fuga de memoria y se deshabilitará la virtualización.
- En Android, no puede haber más de 20 plantillas de datos diferentes por `ListView`.

## <a name="consuming-a-datatemplateselector-in-xaml"></a>Consumo de una instancia de DataTemplateSelector en XAML

En XAML, se pueden crear instancias de `PersonDataTemplateSelector` si se declara como un recurso, como se muestra en el ejemplo de código siguiente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" xmlns:local="clr-namespace:Selector;assembly=Selector" x:Class="Selector.HomePage">
    <ContentPage.Resources>
        <ResourceDictionary>
            <DataTemplate x:Key="validPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <DataTemplate x:Key="invalidPersonTemplate">
                <ViewCell>
                   ...
                </ViewCell>
            </DataTemplate>
            <local:PersonDataTemplateSelector x:Key="personDataTemplateSelector"
                ValidTemplate="{StaticResource validPersonTemplate}"
                InvalidTemplate="{StaticResource invalidPersonTemplate}" />
        </ResourceDictionary>
    </ContentPage.Resources>
  ...
</ContentPage>
```

Este objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de nivel de página define dos instancias de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) y una instancia de `PersonDataTemplateSelector`. La instancia de `PersonDataTemplateSelector` establece sus propiedades `ValidTemplate` y `InvalidTemplate` en las instancias de `DataTemplate` correspondientes mediante la extensión de marcado `StaticResource`. Tenga en cuenta que aunque los recursos se definen en el objeto [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) de la página, también se pueden definir en el nivel de control o aplicación.

La instancia de `PersonDataTemplateSelector` se consume asignándola a la propiedad [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), como se muestra en el ejemplo de código siguiente:

```xaml
<ListView x:Name="listView" ItemTemplate="{StaticResource personDataTemplateSelector}" />
```

En tiempo de ejecución, [`ListView`](xref:Xamarin.Forms.ListView) llama al método `PersonDataTemplateSelector.OnSelectTemplate` para cada uno de los elementos de la colección subyacente, y la llamada pasa el objeto de datos como el parámetro `item`. Después, el elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) devuelto por el método se aplica a ese objeto.

En las capturas de pantalla siguientes se muestra el resultado de la aplicación de `PersonDataTemplateSelector` por parte de [`ListView`](xref:Xamarin.Forms.ListView) a cada objeto de la colección subyacente:

![Objeto ListView con un selector de plantillas de datos](selector-images/data-template-selector.png)

Cualquier objeto `Person` que tenga un valor de propiedad `DateOfBirth` mayor o igual a 1980 se muestra en color verde, y los demás objetos se muestran en color rojo.

## <a name="consuming-a-datatemplateselector-in-cnum"></a>Consumo de una instancia de DataTemplateSelector en C&num;

En C#, se pueden crear instancias de `PersonDataTemplateSelector` y asignarlas a la propiedad [`ListView.ItemTemplate`](xref:Xamarin.Forms.ItemsView`1), como se muestra en el ejemplo de código siguiente:

```csharp
public class HomePageCS : ContentPage
{
  DataTemplate validTemplate;
  DataTemplate invalidTemplate;

  public HomePageCS ()
  {
    ...
    SetupDataTemplates ();
    var listView = new ListView {
      ItemsSource = people,
      ItemTemplate = new PersonDataTemplateSelector {
        ValidTemplate = validTemplate,
        InvalidTemplate = invalidTemplate }
    };

    Content = new StackLayout {
      Margin = new Thickness (20),
      Children = {
        ...
        listView
      }
    };
  }
  ...  
}
```

La instancia de `PersonDataTemplateSelector` establece sus propiedades `ValidTemplate` y `InvalidTemplate` en las instancias de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) correspondientes creadas por el método `SetupDataTemplates`. En tiempo de ejecución, [`ListView`](xref:Xamarin.Forms.ListView) llama al método `PersonDataTemplateSelector.OnSelectTemplate` para cada uno de los elementos de la colección subyacente, y la llamada pasa el objeto de datos como el parámetro `item`. Después, el elemento `DataTemplate` devuelto por el método se aplica a ese objeto.

## <a name="summary"></a>Resumen

En este artículo se ha explicado cómo crear y consumir un elemento [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector). `DataTemplateSelector` se puede usar para elegir un elemento [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) en tiempo de ejecución según el valor de una propiedad enlazada a datos. Esto permite aplicar varias instancias de `DataTemplate` al mismo tipo de objeto para personalizar la apariencia de objetos concretos.

## <a name="related-links"></a>Vínculos relacionados

- [Selector de plantillas de datos (ejemplo)](/samples/xamarin/xamarin-forms-samples/templates-datatemplateselector)
- [DataTemplateSelector](xref:Xamarin.Forms.DataTemplateSelector)