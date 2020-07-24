---
title: Xamarin.FormsSelector
description: El Xamarin.Forms selector muestra una breve lista de elementos, desde la que el usuario puede seleccionar un elemento. En este artículo se explica cómo usar la clase selector para seleccionar un elemento de texto de una lista de datos.
ms.prod: xamarin
ms.assetid: D4815A4B-104B-4294-951B-BD8F2EC33C86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/26/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5e3153e796f26ef150dccc79d8ea6f90127c6a26
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2020
ms.locfileid: "86938689"
---
# <a name="xamarinforms-picker"></a>Xamarin.FormsSelector

_La vista selector es un control para seleccionar un elemento de texto de una lista de datos._

Xamarin.Forms [`Picker`](xref:Xamarin.Forms.Picker) Muestra una lista corta de elementos, desde la que el usuario puede seleccionar un elemento. `Picker` define las siguientes propiedades:

- [`Title`](xref:Xamarin.Forms.Picker.Title)de tipo `string` , cuyo valor predeterminado es `null` .
- `TitleColor`de tipo [`Color`](xref:Xamarin.Forms.Color) , el color usado para mostrar el `Title` texto.
- [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource)de tipo `IList` , la lista de origen de los elementos que se van a mostrar, cuyo valor predeterminado es `null` .
- [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex)de tipo `int` , el índice del elemento seleccionado, cuyo valor predeterminado es-1.
- [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem)de tipo `object` , el elemento seleccionado, cuyo valor predeterminado es `null` .
- [`TextColor`](xref:Xamarin.Forms.Picker.TextColor)de tipo [`Color`](xref:Xamarin.Forms.Color) , el color que se usa para mostrar el texto, cuyo valor predeterminado es [`Color.Default`](xref:Xamarin.Forms.Color.Default) .
- [`FontAttributes`](xref:Xamarin.Forms.Picker.FontAttributes)de tipo [`FontAttributes`](xref:Xamarin.Forms.FontAttributes) , cuyo valor predeterminado es [`FontAtributes.None`](xref:Xamarin.Forms.FontAttributes.None) .
- [`FontFamily`](xref:Xamarin.Forms.Picker.FontFamily)de tipo `string` , cuyo valor predeterminado es `null` .
- [`FontSize`](xref:Xamarin.Forms.Picker.FontSize)de tipo `double` , que tiene como valor predeterminado-1,0.
- `CharacterSpacing`, de tipo `double` , es el espaciado entre los caracteres del elemento mostrado por `Picker` .

Todas las propiedades están respaldadas por [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objetos, lo que significa que se pueden aplicar estilos y las propiedades pueden ser destinos de los enlaces de datos. Las [`SelectedIndex`](xref:Xamarin.Forms.Picker.SelectedIndex) [`SelectedItem`](xref:Xamarin.Forms.Picker.SelectedItem) propiedades y tienen un modo de enlace predeterminado de [`BindingMode.TwoWay`](xref:Xamarin.Forms.BindingMode.TwoWay) , lo que significa que pueden ser destinos de enlaces de datos en una aplicación que usa la arquitectura [Model-View-ViewModel (MVVM)](~/xamarin-forms/enterprise-application-patterns/mvvm.md) . Para obtener información sobre cómo establecer las propiedades de la fuente, vea [fuentes](~/xamarin-forms/user-interface/text/fonts.md).

[`Picker`](xref:Xamarin.Forms.Picker)No muestra ningún dato cuando se muestra por primera vez. En su lugar, el valor de su [`Title`](xref:Xamarin.Forms.Picker.Title) propiedad se muestra como un marcador de posición en las plataformas iOS y Android:

[![Presentación inicial del selector](images/picker-initial.png)](images/picker-initial-large.png#lightbox "Presentación inicial del selector")

Cuando [`Picker`](xref:Xamarin.Forms.Picker) obtiene el foco, se muestran sus datos y el usuario puede seleccionar un elemento:

[![Selector seleccionar un elemento](images/picker-selection.png)](images/picker-selection-large.png#lightbox "Selector seleccionar un elemento")

[`Picker`](xref:Xamarin.Forms.Picker)Desencadena un [`SelectedIndexChanged`](xref:Xamarin.Forms.Picker.SelectedIndexChanged) evento cuando el usuario selecciona un elemento. A continuación, se muestra el elemento seleccionado `Picker` :

![Selector después de la selección](images/picker-after-selection.png)

Hay dos técnicas para rellenar un [`Picker`](xref:Xamarin.Forms.Picker) con datos:

- Establecer la [`ItemsSource`](xref:Xamarin.Forms.Picker.ItemsSource) propiedad en los datos que se van a mostrar. Esta es la técnica recomendada. Para obtener más información, consulte [configuración de la propiedad ItemsSource de un selector](populating-itemssource.md).
- Agregar los datos que se van a mostrar a la [`Items`](xref:Xamarin.Forms.Picker.Items) colección. Esta técnica era el proceso original para rellenar [`Picker`](xref:Xamarin.Forms.Picker) con datos. Para obtener más información, vea [Agregar datos a la colección de elementos de un selector](populating-items.md).

## <a name="related-links"></a>Vínculos relacionados

- [Selector](xref:Xamarin.Forms.Picker)
