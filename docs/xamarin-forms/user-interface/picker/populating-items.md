---
title: Adición de datos a la colección de elementos de un selector
description: La vista de selector es un control para seleccionar un elemento de texto en una lista de datos. En este artículo se explica cómo rellenar un selector de datos, éste se agrega a la colección de elementos y cómo responder a la selección de elementos por el usuario.
ms.prod: xamarin
ms.assetid: 3C840F64-A430-457D-A4B2-3D7AF46F9DBE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
ms.openlocfilehash: 6363f84cb9c947fe8035b51c9f7aed05be6be9e0
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649210"
---
# <a name="adding-data-to-a-pickers-items-collection"></a>Adición de datos a la colección de elementos de un selector

[![Descargar ejemplo](~/media/shared/download.png) descargar el ejemplo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)

_La vista de selector es un control para seleccionar un elemento de texto en una lista de datos. En este artículo se explica cómo rellenar un selector de datos, éste se agrega a la colección de elementos y cómo responder a la selección de elementos por el usuario._

## <a name="populating-a-picker-with-data"></a>Rellenar un selector con datos

Antes de Xamarin.Forms 2.3.4, el proceso para rellenar un [ `Picker` ](xref:Xamarin.Forms.Picker) con datos consistió en agregar los datos que se mostrará como de solo lectura [ `Items` ](xref:Xamarin.Forms.Picker.Items) colección, que es de tipo `IList<string>`. Cada elemento de la colección debe ser de tipo `string`. Se pueden agregar elementos en XAML, inicialice la `Items` propiedad con una lista de `x:String` elementos:

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

Además de agregar datos utilizando el `Items.Add` método, datos también se pueden insertar en la colección utilizando el `Items.Insert` método.

## <a name="responding-to-item-selection"></a>Responder a la selección de elementos

Un [ `Picker` ](xref:Xamarin.Forms.Picker) admite la selección de un elemento a la vez. Cuando un usuario selecciona un elemento, el [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) desencadena el evento y el [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) se actualiza la propiedad en un entero que representa el índice del elemento seleccionado en la lista. El `SelectedIndex` propiedad es un número de base cero que indica el elemento que el usuario seleccionado. Si se selecciona ningún elemento, que es el caso cuando la `Picker` en primer lugar se crea y se inicializa, `SelectedIndex` será -1.

> [!NOTE]
> Elemento de comportamiento de la selección en un [ `Picker` ](xref:Xamarin.Forms.Picker) puede personalizarse en iOS con una plataforma específica. Para obtener más información, consulte [selección de elementos de control de selector de](~/xamarin-forms/platform/ios/picker-selection.md).

El siguiente ejemplo de código muestra la `OnPickerSelectedIndexChanged` método de controlador de eventos, que es ejecutado cuando la [ `SelectedIndexChanged` ](xref:Xamarin.Forms.Picker.SelectedIndexChanged) desencadena el evento:

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

Este método obtiene la [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) valor de propiedad y el valor se utiliza para recuperar el elemento seleccionado de la [ `Items` ](xref:Xamarin.Forms.Picker.Items) colección. Dado que cada elemento de la `Items` colección es un `string`, se pueden mostrar mediante un [ `Label` ](xref:Xamarin.Forms.Label) sin necesidad de realizar una conversión.

> [!NOTE]
> Un [ `Picker` ](xref:Xamarin.Forms.Picker) puede inicializarse para mostrar un elemento específico mediante el establecimiento del [ `SelectedIndex` ](xref:Xamarin.Forms.Picker.SelectedIndex) propiedad. Sin embargo, el `SelectedIndex` propiedad debe establecerse después de inicializar el [ `Items` ](xref:Xamarin.Forms.Picker.Items) colección.

## <a name="related-links"></a>Vínculos relacionados

- [Demostración de selector (ejemplo)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-pickerdemo)
- [Selector](xref:Xamarin.Forms.Picker)
