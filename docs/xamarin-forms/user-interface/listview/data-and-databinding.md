---
title: ''
description: En este artículo se explica cómo rellenar Xamarin.Forms ListView con datos y cómo usar el enlace de datos con un control ListView.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 38a895c9064fc012aec35b37eac78bb16ff009a9
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2020
ms.locfileid: "84131513"
---
# <a name="listview-data-sources"></a>Orígenes de datos de ListView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)

Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) Se usa para mostrar listas de datos. En este artículo se explica cómo rellenar `ListView` con datos y cómo enlazar datos al elemento seleccionado.

## <a name="itemssource"></a>ItemsSource

[`ListView`](xref:Xamarin.Forms.ListView)Se rellena con datos mediante la [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) propiedad, que puede aceptar cualquier colección que implemente `IEnumerable` . La manera más sencilla de rellenar un `ListView` implica el uso de una matriz de cadenas:

```xaml
<ListView>
      <ListView.ItemsSource>
          <x:Array Type="{x:Type x:String}">
            <x:String>mono</x:String>
            <x:String>monodroid</x:String>
            <x:String>monotouch</x:String>
            <x:String>monorail</x:String>
            <x:String>monodevelop</x:String>
            <x:String>monotone</x:String>
            <x:String>monopoly</x:String>
            <x:String>monomodal</x:String>
            <x:String>mononucleosis</x:String>
          </x:Array>
      </ListView.ItemsSource>
</ListView>
```

El código de C# equivalente es el siguiente:

```csharp
var listView = new ListView();
listView.ItemsSource = new string[]
{
  "mono",
  "monodroid",
  "monotouch",
  "monorail",
  "monodevelop",
  "monotone",
  "monopoly",
  "monomodal",
  "mononucleosis"
};
```

![](data-and-databinding-images/itemssource-simple.png "ListView Displaying List of Strings")

Este enfoque rellenará `ListView` con una lista de cadenas. De forma predeterminada, `ListView` llamará a `ToString` y mostrará el resultado en una `TextCell` para cada fila. Para personalizar el modo en que se muestran los datos, vea apariencia de la [celda](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

Dado `ItemsSource` que se ha enviado a una matriz, el contenido no se actualizará a medida que la lista o la matriz subyacentes cambien. Si desea que ListView se actualice automáticamente a medida que se agregan, quitan y cambian elementos en la lista subyacente, deberá utilizar `ObservableCollection` . [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1)se define en `System.Collections.ObjectModel` y es igual `List` que, salvo que puede notificar los `ListView` cambios:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
listView.ItemsSource = employees;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employees.Add(new Employee(){ DisplayName="Mr. Mono"});
```

## <a name="data-binding"></a>Enlace de datos

El enlace de datos es el "pegamento" que enlaza las propiedades de un objeto de interfaz de usuario a las propiedades de algún objeto CLR, como una clase en el ViewModel. El enlace de datos es útil porque simplifica el desarrollo de las interfaces de usuario al reemplazar una gran cantidad de código reutilizable de aburrido.

El enlace de datos funciona manteniendo los objetos sincronizados a medida que cambian sus valores enlazados. En lugar de tener que escribir controladores de eventos para cada vez que cambie el valor de un control, establezca el enlace y habilite el enlace en el ViewModel.

Para obtener más información sobre el enlace de datos, vea [conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) , que es la cuarta parte de la [ Xamarin.Forms serie de artículos conceptos básicos de XAML](~/xamarin-forms/xaml/xaml-basics/index.md).

### <a name="binding-cells"></a>Enlazar celdas

Las propiedades de las celdas (y los elementos secundarios de las celdas) se pueden enlazar a las propiedades de los objetos de `ItemsSource` . Por ejemplo, `ListView` se podría usar una para presentar una lista de empleados.

La clase Employee:

```csharp
public class Employee
{
    public string DisplayName {get; set;}
}
```

`ObservableCollection<Employee>`Se crea un, se establece como `ListView` `ItemsSource` y la lista se rellena con datos:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
public ObservableCollection<Employee> Employees { get { return employees; }}

public EmployeeListPage()
{
    EmployeeView.ItemsSource = employees;

    // ObservableCollection allows items to be added after ItemsSource
    // is set and the UI will react to changes
    employees.Add(new Employee{ DisplayName="Rob Finnerty"});
    employees.Add(new Employee{ DisplayName="Bill Wrestler"});
    employees.Add(new Employee{ DisplayName="Dr. Geri-Beth Hooper"});
    employees.Add(new Employee{ DisplayName="Dr. Keith Joyce-Purdy"});
    employees.Add(new Employee{ DisplayName="Sheri Spruce"});
    employees.Add(new Employee{ DisplayName="Burt Indybrick"});
}
```

> [!WARNING]
> Mientras `ListView` que se va a actualizar en respuesta a los cambios en su subyacente `ObservableCollection` , `ListView` no se actualizará si `ObservableCollection` se asigna una instancia diferente a la `ObservableCollection` referencia original (por ejemplo, `employees = otherObservableCollection;` ).

El siguiente fragmento de código muestra un `ListView` enlazado a una lista de empleados:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:constants="clr-namespace:XamarinFormsSample;assembly=XamarinFormsXamlSample"
             x:Class="XamarinFormsXamlSample.Views.EmployeeListPage"
             Title="Employee List">
  <ListView x:Name="EmployeeView"
            ItemsSource="{Binding Employees}">
    <ListView.ItemTemplate>
      <DataTemplate>
        <TextCell Text="{Binding DisplayName}" />
      </DataTemplate>
    </ListView.ItemTemplate>
  </ListView>
</ContentPage>
```

En este ejemplo de XAML se define un `ContentPage` que contiene un `ListView` . El origen de datos del control `ListView` se establece mediante el atributo `ItemsSource`. El diseño de cada fila de `ItemsSource` se define dentro del elemento `ListView.ItemTemplate`. Esto da como resultado las siguientes capturas de pantallas:

![](data-and-databinding-images/bound-data.png "ListView using Data Binding")

> [!WARNING]
> `ObservableCollection` no es un subproceso seguro. `ObservableCollection`La modificación de hace que las actualizaciones de la interfaz de usuario se realicen en el mismo subproceso que realizó las modificaciones. Si el subproceso no es el subproceso de la interfaz de usuario principal, producirá una excepción.

### <a name="binding-selecteditem"></a>Enlace SelectedItem

A menudo, querrá enlazar al elemento seleccionado de un `ListView` , en lugar de usar un controlador de eventos para responder a los cambios. Para hacer esto en XAML, enlace la `SelectedItem` propiedad:

```xaml
<ListView x:Name="listView"
          SelectedItem="{Binding Source={x:Reference SomeLabel},
          Path=Text}">
 …
</ListView>
```

Suponiendo `listView` que `ItemsSource` es una lista de cadenas, `SomeLabel` tendrá su `Text` propiedad enlazada a `SelectedItem` .

## <a name="related-links"></a>Vínculos relacionados

- [Enlace bidireccional (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
