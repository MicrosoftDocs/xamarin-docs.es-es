---
title: Diseños enlazables en Xamarin.Forms
description: Los diseños enlazables permiten a las clases de diseño generar su contenido enlazando con una colección de elementos, con la opción de establecer la apariencia de cada elemento con un DataTemplate.
ms.prod: xamarin
ms.assetid: 824C3319-20A0-42D0-8632-CDECD98349C3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: a824c892d21df9264b772bed09a4aef893f3b949
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647904"
---
# <a name="bindable-layouts-in-xamarinforms"></a>Diseños enlazables en Xamarin.Forms

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)

Los diseños enlazables permiten que cualquier clase de diseño que deriva de la clase [`Layout<T>`](xref:Xamarin.Forms.Layout`1) genere su contenido al enlazarse a una colección de elementos, con la opción de establecer la apariencia de cada elemento con una [`DataTemplate`](xref:Xamarin.Forms.DataTemplate). Los diseños enlazables los proporciona la clase `BindableLayout`, que expone las siguientes propiedades adjuntas:

- `ItemsSource`: especifica la colección de elementos `IEnumerable` que va a mostrar el diseño.
- `ItemTemplate`: especifica la [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que se aplicará a cada elemento de la colección de elementos que muestra el diseño.
- `ItemTemplateSelector`: especifica el [ `DataTemplateSelector` ](xref:Xamarin.Forms.DataTemplateSelector) que se usará para elegir una [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) para un elemento en tiempo de ejecución.

Estas propiedades se pueden conectar a las clases [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout), [`FlexLayout`](xref:Xamarin.Forms.FlexLayout), [`Grid`](xref:Xamarin.Forms.Grid), [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) y [`StackLayout`](xref:Xamarin.Forms.StackLayout), que derivan de la clase [`Layout<T>`](xref:Xamarin.Forms.Layout`1).

> [!NOTE]
> La propiedad `ItemTemplate` tiene prioridad cuando están establecidas ambas propiedades `ItemTemplate` e `ItemTemplateSelector`.

La clase `Layout<T>` expone una colección [`Children`](xref:Xamarin.Forms.Layout`1.Children), a la que se agregan los elementos secundarios de un diseño. Cuando se establece la propiedad `BinableLayout.ItemsSource` en una colección de elementos y se adjunta a una clase derivada de [`Layout<T>`](xref:Xamarin.Forms.Layout`1), cada elemento de la colección se agrega a la colección `Layout<T>.Children` para que el diseño lo muestre. Después, la clase derivada de `Layout<T>` actualizará sus vistas secundarias cuando cambie la colección subyacente. Para obtener más información sobre el ciclo de diseño de Xamarin.Forms, consulte [Crear un diseño personalizado](~/xamarin-forms/user-interface/layouts/custom.md).

Los diseños enlazables solo deben usarse cuando la colección de elementos que se mostrará es pequeña y no se necesiten el desplazamiento ni la selección. Aunque el desplazamiento puede proporcionarse al encapsular un diseño enlazable en un [`ScrollView`](xref:Xamarin.Forms.ScrollView), no se recomienda, ya que los diseños enlazables carecen de virtualización de interfaz de usuario. Cuando se requiere desplazamiento, se debe usar una vista desplazable que incluya la virtualización [`ListView`](xref:Xamarin.Forms.ListView) de [`CollectionView`](xref:Xamarin.Forms.CollectionView)la interfaz de usuario, como o. Ignorar esta recomendación puede provocar problemas de rendimiento.

> [!IMPORTANT]
>Aunque es técnicamente posible asociar un diseño enlazable a cualquier clase [`Layout<T>`](xref:Xamarin.Forms.Layout`1) de diseño que deriva de la clase, no siempre es práctico hacerlo, especialmente en el caso de las [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout)clases, [`Grid`](xref:Xamarin.Forms.Grid)y. [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) Por ejemplo, considere el escenario en el que desea mostrar una colección de datos en [`Grid`](xref:Xamarin.Forms.Grid) con un diseño enlazable, donde cada elemento de la colección es un objeto que contiene varias propiedades. Cada fila de `Grid` debe mostrar un objeto de la colección, donde cada columna `Grid` de muestra una de las propiedades del objeto. Dado que `Grid` para el diseño enlazable solo puede contener un único objeto, es necesario que ese objeto sea una clase de diseño que contenga varias vistas que muestren una de las propiedades del objeto en una columna específica. [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) Aunque este escenario se puede realizar con diseños enlazables, da como resultado un elemento primario `Grid` que contiene un elemento secundario `Grid` para cada elemento de la colección enlazada, lo que supone un uso muy ineficaz `Grid` y problemático del diseño.

## <a name="populating-a-bindable-layout-with-data"></a>Rellenar un diseño enlazable con datos

Un diseño enlazable se rellena con datos estableciendo la propiedad `ItemsSource` a cualquier colección que implementa `IEnumerable`y adjuntarlo a una clase derivada de [`Layout<T>`](xref:Xamarin.Forms.Layout`1):

```xaml
<Grid BindableLayout.ItemsSource="{Binding Items}" />
```

El código de C# equivalente es:

```csharp
IEnumerable<string> items = ...;
var grid = new Grid();
BindableLayout.SetItemsSource(grid, items);
```

Cuando la propiedad adjunta `BindableLayout.ItemsSource` se establece en un diseño, pero la propiedad adjunta `BindableLayout.ItemTemplate` no se ha establecido, todos los elementos en el `IEnumerable` colección serán mostrados por un [`Label`](xref:Xamarin.Forms.Label) creado por la clase `BindableLayout`.

## <a name="defining-item-appearance"></a>Definir la apariencia del elemento

Se puede definir la apariencia de cada elemento en el diseño enlazable estableciendo la propiedad adjunta `BindableLayout.ItemTemplate` a un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

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

El código de C# equivalente es:

```csharp
DataTemplate circleImageTemplate = ...;
var stackLayout = new StackLayout();
BindableLayout.SetItemsSource(stackLayout, viewModel.User.TopFollowers);
BindableLayout.SetItemTemplate(stackLayout, circleImageTemplate);
```

En este ejemplo, una vista `CircleImage` definida en la  [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) mostrará todos los elementos de la colección `TopFollowers`:

![Diseño enlazable con un DataTemplate](bindable-layouts-images/top-followers.png "Diseño enlazable con una plantilla de datos")

Para obtener más información sobre las plantillas de datos, consulte [Plantillas de datos de Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md).

## <a name="choosing-item-appearance-at-runtime"></a>Elección de la apariencia del elemento en tiempo de ejecución

Se puede elegir la apariencia de cada elemento en el diseño enlazable en tiempo de ejecución, según el valor del elemento, estableciendo la propiedad adjunta `BindableLayout.ItemTemplateSelector` a un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector):

```xaml
<FlexLayout BindableLayout.ItemsSource="{Binding User.FavoriteTech}"
            BindableLayout.ItemTemplateSelector="{StaticResource TechItemTemplateSelector}"
            ... />
```

El código de C# equivalente es:

```csharp
DataTemplateSelector dataTemplateSelector = new TechItemTemplateSelector { ... };
var flexLayout = new FlexLayout();
BindableLayout.SetItemsSource(flexLayout, viewModel.User.FavoriteTech);
BindableLayout.SetItemTemplateSelector(flexLayout, dataTemplateSelector);
```

El [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) usado en la aplicación de ejemplo se muestra en el ejemplo siguiente:

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

La clase `TechItemTemplateSelector` define las propiedades `DefaultTemplate` y `XamarinFormsTemplate` de tipo [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate) que se establecen para diferentes plantillas de datos. El método `OnSelectTemplate`  devuelve el `XamarinFormsTemplate`, que muestra un elemento en rojo oscuro con un corazón junto a él, cuando el elemento es igual a "Xamarin.Forms". Cuando el elemento no es igual a "Xamarin. Forms" `OnSelectTemplate` , el método `DefaultTemplate`devuelve, que muestra un elemento con [`Label`](xref:Xamarin.Forms.Label)el color predeterminado de:

![El diseño se puede enlazar con un DataTemplateSelector](bindable-layouts-images/favorite-tech.png "Diseño enlazable con un selector de plantillas de datos")

Para obtener más información sobre los selectores de plantilla de datos, vea [crear un DataTemplateSelector de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md).

## <a name="related-links"></a>Vínculos relacionados

- [Demostración de diseño enlazable (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-bindablelayouts)
- [Creación de un diseño personalizado](~/xamarin-forms/user-interface/layouts/custom.md)
- [Plantillas de datos de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/index.md)
- [Crear un DataTemplateSelector de Xamarin. Forms](~/xamarin-forms/app-fundamentals/templates/data-templates/selector.md)
