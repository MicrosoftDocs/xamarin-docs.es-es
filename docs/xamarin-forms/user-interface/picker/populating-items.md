---
title: Adición de datos a la colección de elementos de un selector
description: La vista selector es un control para seleccionar un elemento de texto de una lista de datos. En este artículo se explica cómo rellenar un selector con datos agregándolo a la colección de elementos y cómo responder a la selección de elementos por parte del usuario.
ms.prod: xamarin
ms.assetid: 3C840F64-A430-457D-A4B2-3D7AF46F9DBE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8904f3392e935b3d8aa08d87d208bdb46ac37b30
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2020
ms.locfileid: "93368079"
---
# <a name="adding-data-to-a-pickers-items-collection"></a>Adición de datos a la colección de elementos de un selector

[![Descargar ejemplo](~/media/shared/download.png) Descargar el ejemplo](/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)

_La vista selector es un control para seleccionar un elemento de texto de una lista de datos. En este artículo se explica cómo rellenar un selector con datos agregándolo a la colección de elementos y cómo responder a la selección de elementos por parte del usuario._

## <a name="populating-a-picker-with-data"></a>Rellenar un selector con datos

Antes de Xamarin.Forms 2.3.4, el proceso de rellenar un [`Picker`](xref:Xamarin.Forms.Picker) con datos era agregar los datos que se van a mostrar a la colección de solo lectura [`Items`](xref:Xamarin.Forms.Picker.Items) , que es de tipo `IList<string>` . Cada elemento de la colección debe ser de tipo `string` . Los elementos se pueden agregar en XAML inicializando la `Items` propiedad con una lista de `x:String` elementos:

```xaml
<Picker Title="Select a monkey"
        TitleColor="Red">
  <Picker.Items>
    <x:String>Baboon</x:String>
    <x:String>Capuchin Monkey</x:String>
    <x:String>Blue Monkey</x:String>
    <x:String>Squirrel Monkey</x:String>
    <x:String>Golden Lion Tamarin</x:String>
    <x:String>Howler Monkey</x:String>
    <x:String>Japanese Macaque</x:String>
  </Picker.Items>
</Picker>
```

El código de C# equivalente se muestra a continuación:

```csharp
var picker = new Picker { Title = "Select a monkey", TitleColor = Color.Red };
picker.Items.Add("Baboon");
picker.Items.Add("Capuchin Monkey");
picker.Items.Add("Blue Monkey");
picker.Items.Add("Squirrel Monkey");
picker.Items.Add("Golden Lion Tamarin");
picker.Items.Add("Howler Monkey");
picker.Items.Add("Japanese Macaque");
```

Además de agregar datos mediante el `Items.Add` método, los datos también se pueden insertar en la colección mediante el `Items.Insert` método.

## <a name="responding-to-item-selection"></a>Responder a la selección de elementos

[`Picker`](xref:Xamarin.Forms.Picker)Permite seleccionar un elemento cada vez. Cuando un usuario selecciona un elemento, el [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento se activa y la [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) propiedad se actualiza a un entero que representa el índice del elemento seleccionado en la lista. La `SelectedIndex` propiedad es un número basado en cero que indica el elemento que el usuario seleccionó. Si no hay ningún elemento seleccionado, lo que sucede cuando `Picker` se crea y se inicializa por primera vez, `SelectedIndex` será-1.

> [!NOTE]
> El comportamiento de la selección de elementos en un [`Picker`](xref:Xamarin.Forms.Picker) puede personalizarse en Ios con una plataforma específica. Para obtener más información, vea [controlar la selección de elementos de selector](~/xamarin-forms/platform/ios/picker-selection.md).

En el ejemplo de código siguiente `OnPickerSelectedIndexChanged` se muestra el método de control de eventos, que se ejecuta cuando se [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) activa el evento:

```csharp
void OnPickerSelectedIndexChanged(object sender, EventArgs e)
{
  var picker = (Picker)sender;
  int selectedIndex = picker.SelectedIndex;

  if (selectedIndex != -1)
  {
    monkeyNameLabel.Text = picker.Items[selectedIndex];
  }
}
```

Este método obtiene el [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) valor de propiedad y utiliza el valor para recuperar el elemento seleccionado de la [`Items`](xref:Xamarin.Forms.Picker.Items) colección. Dado que cada elemento de la `Items` colección es `string` , se puede mostrar mediante una [`Label`](xref:Xamarin.Forms.Label) sin necesidad de una conversión.

> [!NOTE]
> [`Picker`](xref:Xamarin.Forms.Picker)Se puede inicializar para mostrar un elemento específico estableciendo la [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) propiedad. Sin embargo, la `SelectedIndex` propiedad debe establecerse después de inicializar la [`Items`](xref:Xamarin.Forms.Picker.Items) colección.

## <a name="related-links"></a>Vínculos relacionados

- [Demo de selector (ejemplo)](/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [Selector](xref:Xamarin.Forms.Picker)