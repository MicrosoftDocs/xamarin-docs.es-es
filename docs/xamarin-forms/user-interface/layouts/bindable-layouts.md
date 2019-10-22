---
title: Diseños enlazables en Xamarin. Forms
description: Los diseños enlazables permiten a las clases de diseño generar su contenido enlazando con una colección de elementos, con la opción de establecer la apariencia de cada elemento con un DataTemplate.
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: a824c892d21df9264b772bed09a4aef893f3b949
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/21/2019
ms.locfileid: "68647904"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Diseños enlazables en Xamarin. Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

Los diseños enlazables permiten que cualquier clase de diseño que derive de la clase [`Layout<T>`](xref:Xamarin.Forms.Layout`1) genere su contenido enlazando a una colección de elementos, con la opción de establecer la apariencia de cada elemento con un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Los diseños enlazables se proporcionan mediante la clase `BindableLayout`, que expone las siguientes propiedades adjuntas:

- `ItemsSource`: especifica la colección de elementos `IEnumerable` que va a mostrar el diseño.
- `ItemTemplate`: especifica el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que se va a aplicar a cada elemento de la colección de elementos mostrados por el diseño.
- `ItemTemplateSelector`: especifica el [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) que se usará para elegir un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) para un elemento en tiempo de ejecución.

Estas propiedades se pueden adjuntar a las clases [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), [`FlexLayout`](xref:Xamarin.Forms.FlexLayout), [`Grid`](xref:Xamarin.Forms.Grid), [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout)y [`StackLayout`](xref:Xamarin.Forms.StackLayout) , que se derivan de la clase [1](xref:Xamarin.Forms.Layout`1) .

> [!NOTE]
> La propiedad `ItemTemplate` tiene prioridad cuando se establecen las propiedades `ItemTemplate` y `ItemTemplateSelector`.

La clase `Layout<T>` expone una colección [`Children`](xref:Xamarin.Forms.Layout`1.Children) , a la que se agregan los elementos secundarios de un diseño. Cuando la propiedad `BinableLayout.ItemsSource` está establecida en una colección de elementos y se adjunta a una clase derivada de [`Layout<T>`](xref:Xamarin.Forms.Layout`1), cada elemento de la colección se agrega a la colección `Layout<T>.Children` para que la muestre el diseño. La clase derivada de `Layout<T>` actualizará sus vistas secundarias cuando cambie la colección subyacente. Para obtener más información sobre el ciclo de diseño de Xamarin. Forms, vea [crear un diseño personalizado](~/xamarin-forms/user-interface/layouts/custom.md).

Los diseños enlazables solo deben usarse cuando la colección de elementos que se va a mostrar es pequeña, y no se requiere desplazamiento y selección. Aunque el desplazamiento se puede proporcionar encapsulando un diseño enlazable en una [`ScrollView`](xref:Xamarin.Forms.ScrollView), no se recomienda que los diseños enlazables carezcan de la virtualización de la interfaz de usuario. Cuando se requiere desplazamiento, se debe usar una vista desplazable que incluya la virtualización de la interfaz de usuario, como [`ListView`](xref:Xamarin.Forms.ListView) o [`CollectionView`](xref:Xamarin.Forms.CollectionView). Si no se observa esta recomendación, pueden producirse problemas de rendimiento.

> [!IMPORTANT]
>Aunque es técnicamente posible asociar un diseño enlazable a cualquier clase de diseño que deriva de la clase [`Layout<T>`](xref:Xamarin.Forms.Layout`1) , no siempre es práctico hacerlo, especialmente en el caso de las clases [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), [`Grid`](xref:Xamarin.Forms.Grid)y [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) . Por ejemplo, considere el escenario en el que desea mostrar una colección de datos en un [`Grid`](xref:Xamarin.Forms.Grid) mediante un diseño enlazable, donde cada elemento de la colección es un objeto que contiene varias propiedades. Cada fila del `Grid` debe mostrar un objeto de la colección, donde cada columna del `Grid` muestra una de las propiedades del objeto. Dado que el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) del diseño enlazable solo puede contener un único objeto, es necesario que ese objeto sea una clase de diseño que contenga varias vistas, cada una de las cuales mostrará una de las propiedades del objeto en una columna de `Grid` específica. Aunque este escenario se puede realizar con diseños enlazables, da como resultado un `Grid` primario que contiene un `Grid` secundario para cada elemento de la colección enlazada, lo que supone un uso muy ineficaz y problemático del diseño de `Grid`.

## <a name="populating-a-bindable-layout-with-data"></a>Rellenar un diseño enlazable con datos

Un diseño enlazable se rellena con datos estableciendo su propiedad `ItemsSource` en cualquier colección que implemente `IEnumerable` y asócielo a una clase derivada de [`Layout<T>`](xref:Xamarin.Forms.Layout`1):

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

El código de C# equivalente es el siguiente:

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

Cuando se establece el `BindableLayout.ItemsSource` propiedad adjunta en un diseño, pero no se establece la propiedad `BindableLayout.ItemTemplate` adjunta, se mostrarán todos los elementos de la colección de `IEnumerable` en un [`Label`](xref:Xamarin.Forms.Label) creado por la clase `BindableLayout`.

## <a name="defining-item-appearance"></a>Definir la apariencia del elemento

La apariencia de cada elemento en el diseño enlazable se puede definir estableciendo el `BindableLayout.ItemTemplate` propiedad adjunta en un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<StackLayout BindableLayout.ItemsSource="{Binding User.TopFollowers}"
             Orientation="Horizontal"
             ...>
    <BindableLayout.ItemTemplate>
        <DataTemplate>
            <controls:CircleImage Source="{Binding}"
                                  Aspect="AspectFill"
                                  WidthRequest="44"
                                  HeightRequest="44"
                                  ... />
        </DataTemplate>
    </BindableLayout.ItemTemplate>
</StackLayout>
```

El código de C# equivalente es el siguiente:

```csharp
DataTemplate circleImageTemplate = ...;
var stackLayout = new StackLayout();
BindableLayout.SetItemsSource(stackLayout, viewModel.User.TopFollowers);
BindableLayout.SetItemTemplate(stackLayout, circleImageTemplate);
```

En este ejemplo, cada elemento de la colección de `TopFollowers` se mostrará en una vista de `CircleImage` definida en el [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

![Diseño enlazable con un DataTemplate](bindable-layouts-images/top-followers.png "Diseño enlazable con una plantilla de datos")

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choosing-item-appearance-at-runtime"></a>Elección de la apariencia del elemento en tiempo de ejecución

La apariencia de cada elemento en el diseño enlazable se puede elegir en tiempo de ejecución, en función del valor del elemento, al establecer la propiedad `BindableLayout.ItemTemplateSelector` adjunta en un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector):

```xaml
<FlexLayout BindableLayout.ItemsSource="{Binding User.FavoriteTech}"
            BindableLayout.ItemTemplateSelector="{StaticResource TechItemTemplateSelector}"
            ... />
```

El código de C# equivalente es el siguiente:

```csharp
DataTemplateSelector dataTemplateSelector = new TechItemTemplateSelector { ... };
var flexLayout = new FlexLayout();
BindableLayout.SetItemsSource(flexLayout, viewModel.User.FavoriteTech);
BindableLayout.SetItemTemplateSelector(flexLayout, dataTemplateSelector);
```

El [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) utilizado en la aplicación de ejemplo se muestra en el ejemplo siguiente:

```csharp
public class TechItemTemplateSelector : DataTemplateSelector
{
    public DataTemplate DefaultTemplate { get; set; }
    public DataTemplate XamarinFormsTemplate { get; set; }

    protected override DataTemplate OnSelectTemplate(object item, BindableObject container)
    {
        return (string)item == "Xamarin.Forms" ? XamarinFormsTemplate : DefaultTemplate;
    }
}
```

La clase `TechItemTemplateSelector` define `DefaultTemplate` y `XamarinFormsTemplate` propiedades de [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) que se establecen en distintas plantillas de datos. El método `OnSelectTemplate` devuelve el `XamarinFormsTemplate`, que muestra un elemento en rojo oscuro con un corazón junto a él, cuando el elemento es igual a "Xamarin. Forms". Cuando el elemento no es igual a "Xamarin. Forms", el método `OnSelectTemplate` devuelve el `DefaultTemplate`, que muestra un elemento con el color predeterminado de un [`Label`](xref:Xamarin.Forms.Label):

![Diseño enlazable con un DataTemplateSelector](bindable-layouts-images/favorite-tech.png "Diseño enlazable con un selector de plantillas de datos")

Para obtener más información sobre los selectores de plantilla de datos, vea [crear un DataTemplateSelector de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Vínculos relacionados

- [Demostración de diseño enlazable (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Creación de un diseño personalizado](~/xamarin-forms/user-interface/layouts/custom.md)
- [Plantillas de datos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Crear un DataTemplateSelector de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
