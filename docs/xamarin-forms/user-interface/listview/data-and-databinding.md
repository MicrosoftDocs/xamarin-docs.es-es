---
title: Orígenes de datos de ListView
description: En este artículo se explica cómo rellenar el ListView de Xamarin.Forms con datos y cómo usar el enlace de datos con un ListView.
ms.prod: xamarin
ms.assetid: B5571660-1E82-4379-95C3-0725288CF5D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2020
ms.openlocfilehash: e51f0bd011750b030c0a11b9b89a2c2473f2a9ed
ms.sourcegitcommit: d83c6af42ed26947aa7c0ecfce00b9ef60f33319
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247592"
---
# <a name="listview-data-sources"></a>Orígenes de datos de ListView

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)

Un [`ListView`](xref:Xamarin.Forms.ListView) de Xamarin. Forms se usa para mostrar listas de datos. En este artículo se explica cómo rellenar un `ListView` con datos y cómo enlazar datos al elemento seleccionado.

## <a name="itemssource"></a>ItemsSource

Un [`ListView`](xref:Xamarin.Forms.ListView) se rellena con datos mediante la propiedad [`ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) , que puede aceptar cualquier colección que implemente `IEnumerable`. La manera más sencilla de rellenar un `ListView` implica el uso de una matriz de cadenas:

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

Este enfoque rellenará el `ListView` con una lista de cadenas. De forma predeterminada, `ListView` llamará a `ToString` y mostrará el resultado en un `TextCell` para cada fila. Para personalizar el modo en que se muestran los datos, vea apariencia de la [celda](~/xamarin-forms/user-interface/listview/customizing-cell-appearance.md).

Dado que `ItemsSource` se ha enviado a una matriz, el contenido no se actualizará a medida que la lista o la matriz subyacentes cambien. Si desea que ListView se actualice automáticamente a medida que se agregan, quitan y cambian elementos en la lista subyacente, deberá usar una `ObservableCollection`. [`ObservableCollection`](xref:System.Collections.ObjectModel.ObservableCollection`1) se define en `System.Collections.ObjectModel` y es igual que `List`, salvo que puede notificar a `ListView` de cualquier cambio:

```csharp
ObservableCollection<Employee> employees = new ObservableCollection<Employee>();
listView.ItemsSource = employees;

//Mr. Mono will be added to the ListView because it uses an ObservableCollection
employees.Add(new Employee(){ DisplayName="Mr. Mono"});
```

## <a name="data-binding"></a>Enlace de datos

El enlace de datos es el "pegamento" que enlaza las propiedades de un objeto de interfaz de usuario a las propiedades de algún objeto CLR, como una clase en el ViewModel. Enlace de datos es útil porque simplifica el desarrollo de interfaces de usuario mediante la sustitución mucha aburrido código reutilizable.

Enlace de datos funciona manteniendo los objetos sincronizados a medida que cambian sus valores enlazados. En lugar de tener que escribir controladores de eventos para cada vez que cambie el valor de un control, establezca el enlace y habilite el enlace en el ViewModel.

Para obtener más información sobre el enlace de datos, vea [conceptos básicos del enlace de datos](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md) , que es la parte cuatro de la [serie de artículos de conceptos básicos de XAML de Xamarin. Forms](~/xamarin-forms/xaml/xaml-basics/index.md).

### <a name="binding-cells"></a>Enlazar celdas

Las propiedades de las celdas (y los elementos secundarios de las celdas) se pueden enlazar a las propiedades de los objetos en el `ItemsSource`. Por ejemplo, un `ListView` podría usarse para presentar una lista de empleados.

La clase employee:

```csharp
public class Employee
{
    public string DisplayName {get; set;}
}
```

Se crea un `ObservableCollection<Employee>`, se establece como el `ItemsSource`de `ListView` y la lista se rellena con datos:

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
> Aunque una `ListView` se actualizará en respuesta a los cambios en su `ObservableCollection`subyacente, un `ListView` no se actualizará si se asigna una instancia de `ObservableCollection` diferente a la referencia de `ObservableCollection` original (por ejemplo, `employees = otherObservableCollection;`).

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

En este ejemplo de XAML se define una `ContentPage` que contiene un `ListView`. El origen de datos del `ListView` se establece mediante el atributo `ItemsSource`. El diseño de cada fila del `ItemsSource` se define dentro del elemento `ListView.ItemTemplate`. Esto da como resultado las siguientes capturas de pantallas:

![](data-and-databinding-images/bound-data.png "ListView using Data Binding")

> [!WARNING]
> `ObservableCollection` no es seguro para subprocesos. La modificación de un `ObservableCollection` hace que las actualizaciones de la interfaz de usuario se realicen en el mismo subproceso que realizó las modificaciones. Si el subproceso no es el subproceso de la interfaz de usuario principal, producirá una excepción.

### <a name="binding-selecteditem"></a>Enlace SelectedItem

A menudo, querrá enlazar al elemento seleccionado de un `ListView`, en lugar de usar un controlador de eventos para responder a los cambios. Para hacer esto en XAML, enlace la propiedad `SelectedItem`:

```xaml
<ListView x:Name="listView"
          SelectedItem="{Binding Source={x:Reference SomeLabel},
          Path=Text}">
 …
</ListView>
```

Suponiendo que la `ItemsSource` de `listView`sea una lista de cadenas, `SomeLabel` tendrá su propiedad `Text` enlazada al `SelectedItem`.

## <a name="related-links"></a>Vínculos relacionados

- [Enlace bidireccional (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-listview-switchentrytwobinding)
