---
title: Configuración de la propiedad ItemsSource de un selector
description: La vista selector es un control para seleccionar un elemento de texto de una lista de datos. En este artículo se explica cómo rellenar un selector con datos estableciendo la propiedad ItemsSource y cómo responder a la selección de elementos por parte del usuario.
ms.prod: xamarin
ms.assetid: 8ECF390C-9DB2-4441-B9A3-101AE7E5AEC5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 395536752fb0d581408d0b8f91ef623d926aeeb4
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91559550"
---
# <a name="setting-a-pickers-itemssource-property"></a>Configuración de la propiedad ItemsSource de un selector

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)

_La vista selector es un control para seleccionar un elemento de texto de una lista de datos. En este artículo se explica cómo rellenar un selector con datos estableciendo la propiedad ItemsSource y cómo responder a la selección de elementos por parte del usuario._

Xamarin.Forms 2.3.4 ha mejorado la [`Picker`](xref:Xamarin.Forms.Picker) vista agregando la capacidad de rellenarla con datos estableciendo su [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) propiedad y recuperando el elemento seleccionado de la [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) propiedad. Además, se puede cambiar el color del texto del elemento seleccionado estableciendo la [`TextColor`](xref:Xamarin.Forms.Picker.TextColor) propiedad en [`Color`](xref:Xamarin.Forms.Color) .

## <a name="populating-a-picker-with-data"></a>Rellenar un selector con datos

Un [`Picker`](xref:Xamarin.Forms.Picker) se puede rellenar con datos estableciendo su [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) propiedad en una `IList` colección. Cada elemento de la colección debe ser de tipo, o se puede derivar de él `object` . Los elementos se pueden agregar en XAML inicializando la `ItemsSource` propiedad desde una matriz de elementos:

```xaml
<Picker x:Name="picker"
        Title="Select a monkey"
        TitleColor="Red">
  <Picker.ItemsSource>
    <x:Array Type="{x:Type x:String}">
      <x:String>Baboon</x:String>
      <x:String>Capuchin Monkey</x:String>
      <x:String>Blue Monkey</x:String>
      <x:String>Squirrel Monkey</x:String>
      <x:String>Golden Lion Tamarin</x:String>
      <x:String>Howler Monkey</x:String>
      <x:String>Japanese Macaque</x:String>
    </x:Array>
  </Picker.ItemsSource>
</Picker>
```

> [!NOTE]
> Tenga en cuenta que el elemento `x:Array` requiere un atributo `Type` que indica el tipo de los elementos de la matriz.

A continuación se muestra el código de C# equivalente:

```csharp
var monkeyList = new List<string>();
monkeyList.Add("Baboon");
monkeyList.Add("Capuchin Monkey");
monkeyList.Add("Blue Monkey");
monkeyList.Add("Squirrel Monkey");
monkeyList.Add("Golden Lion Tamarin");
monkeyList.Add("Howler Monkey");
monkeyList.Add("Japanese Macaque");

var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.ItemsSource = monkeyList;
```

## <a name="responding-to-item-selection"></a>Responder a la selección de elementos

[`Picker`](xref:Xamarin.Forms.Picker)Permite seleccionar un elemento cada vez. Cuando un usuario selecciona un elemento, [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) se activa el evento, la [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) propiedad se actualiza a un entero que representa el índice del elemento seleccionado en la lista y la [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) propiedad se actualiza a que `object` representa el elemento seleccionado. La [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) propiedad es un número basado en cero que indica el elemento seleccionado por el usuario. Si no hay ningún elemento seleccionado, lo que sucede cuando [`Picker`](xref:Xamarin.Forms.Picker) se crea y se inicializa por primera vez, `SelectedIndex` será-1.

> [!NOTE]
> El comportamiento de la selección de elementos en un [`Picker`](xref:Xamarin.Forms.Picker) puede personalizarse en Ios con una plataforma específica. Para obtener más información, vea [controlar la selección de elementos de selector](~/xamarin-forms/platform/ios/picker-selection.md).

En el ejemplo de código siguiente se muestra cómo recuperar el [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) valor de propiedad de [`Picker`](xref:Xamarin.Forms.Picker) en XAML:

```xaml
<Label Text="{Binding Source={x:Reference picker}, Path=SelectedItem}" />
```

A continuación se muestra el código de C# equivalente:

```csharp
var monkeyNameLabel = new Label();
monkeyNameLabel.SetBinding(Label.TextProperty, new Binding("SelectedItem", source: picker));
```

Además, se puede ejecutar un controlador de eventos cuando el [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento se activa:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = (string)picker.ItemsSource[selectedIndex];
  }
}
```

Este método obtiene el [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) valor de propiedad y utiliza el valor para recuperar el elemento seleccionado de la [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) colección. Es funcionalmente equivalente a recuperar el elemento seleccionado de la [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) propiedad. Tenga en cuenta que cada elemento de la `ItemsSource` colección es de tipo `object` y, por lo tanto, se debe convertir en `string` para mostrarlo.

> [!NOTE]
> [`Picker`](xref:Xamarin.Forms.Picker)Se puede inicializar para mostrar un elemento específico mediante el establecimiento de [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) las [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) propiedades o. Sin embargo, estas propiedades deben establecerse después de inicializar la [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) colección.

## <a name="populating-a-picker-with-data-using-data-binding"></a>Rellenar un selector con datos mediante el enlace de datos

[`Picker`](xref:Xamarin.Forms.Picker)También se puede rellenar con datos mediante el enlace de datos para enlazar su [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) propiedad a una `IList` colección. En XAML, esto se consigue con la [`Binding`](xref:Xamarin.Forms.Xaml.BindingExtension) extensión de marcado:

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}" />
```

A continuación se muestra el código de C# equivalente:

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.ItemDisplayBinding = new Binding("Name");
```

Los [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) datos de la propiedad se enlazan a la `Monkeys` propiedad del modelo de vista conectado, que devuelve una `IList<Monkey>` colección. En el ejemplo de código siguiente se muestra la `Monkey` clase, que contiene cuatro propiedades:

```csharp
public class Monkey
{
  public string Name { get; set; }
  public string Location { get; set; }
  public string Details { get; set; }
  public string ImageUrl { get; set; }
}
```

Al enlazar a una lista de objetos, [`Picker`](xref:Xamarin.Forms.Picker) se debe indicar qué propiedad se va a mostrar de cada objeto. Esto se consigue estableciendo la [`ItemDisplayBinding`](xref:Xamarin.Forms.Picker.ItemDisplayBinding) propiedad en la propiedad necesaria de cada objeto. En los ejemplos de código anteriores, `Picker` se establece para mostrar cada `Monkey.Name` valor de propiedad.

### <a name="responding-to-item-selection"></a>Responder a la selección de elementos

El enlace de datos se puede usar para establecer un objeto en el [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) valor de propiedad cuando cambia:

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red"
        ItemsSource="{Binding Monkeys}"
        ItemDisplayBinding="{Binding Name}"
        SelectedItem="{Binding SelectedMonkey}" />
<Label Text="{Binding SelectedMonkey.Name}" ... />
<Label Text="{Binding SelectedMonkey.Location}" ... />
<Image Source="{Binding SelectedMonkey.ImageUrl}" ... />
<Label Text="{Binding SelectedMonkey.Details}" ... />
```

A continuación se muestra el código de C# equivalente:

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.SetBinding(Picker.ItemsSourceProperty, "Monkeys");
picker.SetBinding(Picker.SelectedItemProperty, "SelectedMonkey");
picker.ItemDisplayBinding = new Binding("Name");

var nameLabel = new Label { ... };
nameLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Name");

var locationLabel = new Label { ... };
locationLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Location");

var image = new Image { ... };
image.SetBinding(Image.SourceProperty, "SelectedMonkey.ImageUrl");

var detailsLabel = new Label();
detailsLabel.SetBinding(Label.TextProperty, "SelectedMonkey.Details");
```

Los [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) datos de la propiedad se enlazan a la `SelectedMonkey` propiedad del modelo de vista conectado, que es de tipo `Monkey` . Por lo tanto, cuando el usuario selecciona un elemento en [`Picker`](xref:Xamarin.Forms.Picker) , la `SelectedMonkey` propiedad se establecerá en el `Monkey` objeto seleccionado. Los `SelectedMonkey` datos del objeto se muestran en la interfaz de usuario por las [`Label`](xref:Xamarin.Forms.Label) [`Image`](xref:Xamarin.Forms.Image) vistas y:

![Selección de elementos de selector](populating-itemssource-images/monkeys.png)

> [!NOTE]
> Tenga en cuenta que las [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) propiedades y admiten enlaces bidireccionales de forma predeterminada.

## <a name="related-links"></a>Vínculos relacionados

- [Demo de selector (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [Aplicación Monkey (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-monkeyapppicker)
- [Selector enlazable (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-bindablepicker)
- [API del selector](xref:Xamarin.Forms.Picker)